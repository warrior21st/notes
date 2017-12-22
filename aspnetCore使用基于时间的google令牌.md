## aspnetCore使用基于时间的google令牌
#### 依赖
- Microsoft.AspNetCore.All 2.0.0
- OtpSharp.Core 1.0.0
#### GoogleAuthenticatorTokenProvider
	public class GoogleAuthenticatorTokenProvider : TotpSecurityStampBasedTokenProvider<DexUser>
    {
        public override Task<string> GenerateAsync(string purpose, UserManager<DexUser> manager, DexUser user)
        {
            return Task.FromResult((string)null);
        }

        public override Task<bool> ValidateAsync(string purpose, string token, UserManager<DexUser> manager, DexUser user)
        {
            Totp otp = null;
            long timeStepMatched = 0;
            var secretKey = user.GetGoogleAuthenticatorSecretKey();
            otp = new Totp(secretKey);
            //using (var ms = new MemoryStream())
            //{
            //    var buffer = Encoding.UTF8.GetBytes(user.PasswordHash);
            //    var i = new Base64Encoder().Decode(buffer, 0, buffer.Length, ms);
            //    ms.Position = 0;
            //    otp = new Totp(ms.ToArray());
            //}

            bool valid = otp.VerifyTotp(token, out timeStepMatched, new VerificationWindow(2, 2));

            return Task.FromResult(valid);
        }

        public Task NotifyAsync(string token, UserManager<DexUser> manager, DexUser user)
        {
            return Task.FromResult(true);
        }

        public override Task<bool> CanGenerateTwoFactorTokenAsync(UserManager<DexUser> manager, DexUser user)
        {
            return Task.FromResult(true);
        }
    }

#### AuthenticatorHelper
	public static class AuthenticatorHelper
    {
        /// <summary>
        /// 获取google认证器secretkey
        /// </summary>
        /// <returns></returns>
        public static byte[] GetGoogleAuthenticatorSecretKeyBytes(string base64SecretKey)
        {
            return Convert.FromBase64String(base64SecretKey);
            //byte[] result = null;
            //using (var ms = new MemoryStream())
            //{
            //    var bytes = Convert.FromBase64String(base64SecretKey);
            //    var i = new Base64Encoder().Encode(bytes, 0, bytes.Length, ms);
            //    ms.Position = 0;
            //    result = ms.ToArray();
            //}

            //return result;
        }

        /// <summary>
        /// 生成google认证器secretkey
        /// </summary>
        /// <returns></returns>
        public static string GenerateNewGoogleAuthenticatorSecretKey()
        {
            var secretKey = KeyGeneration.GenerateRandomKey(20);
            return Convert.ToBase64String(secretKey);
        }

        /// <summary>
        /// 验证google认证器token
        /// </summary>
        /// <param name="user"></param>
        /// <param name="token"></param>
        /// <returns></returns>
        public static bool ValidateGoogleAuthenticatorToken(DexUser user,string token)
        {
            Totp otp = null;
            long timeStepMatched = 0;
            var secretKey = user.GetGoogleAuthenticatorSecretKey();
            otp = new Totp(secretKey);
            //using (var ms = new MemoryStream())
            //{
            //    var buffer = Encoding.UTF8.GetBytes(user.PasswordHash);
            //    var i = new Base64Encoder().Decode(buffer, 0, buffer.Length, ms);
            //    ms.Position = 0;
            //    otp = new Totp(ms.ToArray());
            //}

            bool valid = otp.VerifyTotp(token, out timeStepMatched, new VerificationWindow(2, 2));

            return valid;
        }
    }

#### DexUserExtensions
    /// <summary>
    /// 用户扩展类
    /// </summary>
    public static class DexUserExtensions
    {
        /// <summary>
        /// 获取google认证器secretkey
        /// </summary>
        /// <param name="user"></param>
        /// <returns></returns>
        public static byte[] GetGoogleAuthenticatorSecretKey(this DexUser user)
        {
            return AuthenticatorHelper.GetGoogleAuthenticatorSecretKeyBytes(user.GoogleAuthenticatorSecretKey);
        }
    }

#### AccountController
    public class AccountController : BaseController
    {
        private readonly IUserTwoFactorTokenProvider<DexUser> _userTwoFactorTokenProvider;
        private readonly SignInManager<DexUser> _signInManager;
        private readonly UserManager<DexUser> _userManager;
        private readonly IConfiguration _configuration;
        private readonly IUserClaimsPrincipalFactory<DexUser> _userClaimsPrincipalFactory;
        private readonly LoginLogService _loginLogService;

        public AccountController(ILogger<AccountController> logger, IServiceProvider serviceProvider,
            IUserTwoFactorTokenProvider<DexUser> userTwoFactorTokenProvider,
            SignInManager<DexUser> signInManager,
            UserManager<DexUser> userManager,
            IUserClaimsPrincipalFactory<DexUser> userClaimsPrincipalFactory,
            IConfiguration configuration,
            LoginLogService loginLogService) : base(logger, serviceProvider)
        {
            _userTwoFactorTokenProvider = userTwoFactorTokenProvider;
            _signInManager = signInManager;
            _userManager = userManager;
            _userClaimsPrincipalFactory = userClaimsPrincipalFactory;
            _configuration = configuration;
            _loginLogService = loginLogService;
        }

		/// <summary>
        /// 验证登陆token
        /// </summary>
        /// <param name="userName"></param>
        /// <param name="token"></param>
        /// <param name="rememberMe"></param>
        /// <returns></returns>
        [AllowAnonymous]
        [HttpPost]
        public async Task<JsonResult> LoginByToken(string userName, string token, bool rememberMe)
        {            
            var user = DbContext.Users.SingleOrDefault(x => !x.IsDisabled && x.UserName == userName);
            if (user == null)
                return JsonBusinessErrorResult("用户名不存在");
            var b = await _userTwoFactorTokenProvider.ValidateAsync(null, token, _userManager, user);
            if (b)
            {
                await _signInManager.SignInAsync(user, rememberMe);
                _loginLogService.AddLoginLog(userName, ServerHelper.GetClientIP(HttpContext), ServerHelper.GetClientUserAgent(HttpContext));
            }

            return JsonSuccessResult(b);
        }

        /// <summary>
        /// 注销
        /// </summary>
        /// <returns></returns>
        public async Task<IActionResult> Logout()
        {
            await HttpContext.SignOutAsync(IdentityConstants.ApplicationScheme);
            await HttpContext.SignOutAsync(IdentityConstants.ExternalScheme);
            await HttpContext.SignOutAsync(IdentityConstants.TwoFactorUserIdScheme);

            return RedirectToAction(nameof(Login));
        }

	/// <summary>
        /// 获取令牌验证器二维码地址
        /// </summary>
        /// <param name="userName"></param>
        /// <param name="password"></param>
        /// <returns></returns>
        [AllowAnonymous]
        [HttpGet]
        public async Task<JsonResult> GetAuthenticatorUri(string userName, string password)
        {
            if (string.IsNullOrWhiteSpace(userName))
                return JsonParamsErrorResult(nameof(userName));
            if (string.IsNullOrWhiteSpace(password))
                return JsonParamsErrorResult(nameof(password));

            var user = DbContext.Users.SingleOrDefault(x => x.UserName == userName);
            if (user == null)
                return JsonBusinessErrorResult("用户名不存在");
            if (user.IsDisabled)
                return JsonBusinessErrorResult("用户已被禁用");
            var singResult = await _signInManager.CheckPasswordSignInAsync(user, password, false);
            if (!singResult.Succeeded)
                return JsonBusinessErrorResult("密码不正确");

            if (string.IsNullOrWhiteSpace(user.GoogleAuthenticatorSecretKey))
            {
                user.GoogleAuthenticatorSecretKey = AuthenticatorHelper.GenerateNewGoogleAuthenticatorSecretKey();
                DbContext.Update(user);
                await DbContext.SaveChangesAsync();
            }

            var barcodeUrl = string.Empty;
            var secretKey = user.GetGoogleAuthenticatorSecretKey();
            barcodeUrl = $"{KeyUrl.GetTotpUrl(secretKey, userName)}&issuer={_configuration["SiteConfig:ProgramName"]}";

            return JsonSuccessResult(barcodeUrl);
        }
	}

#### startup
	public void ConfigureServices(IServiceCollection services)
    {
        services.AddScoped<IUserTwoFactorTokenProvider<DexUser>, GoogleAuthenticatorTokenProvider>();
	}
