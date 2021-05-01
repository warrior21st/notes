## aspnetcore totp
#### packages
- OtpSharp.Core 1.0.0

#### TotpHelper
    public class TotpHelper
    {
        /// <summary>
        /// 生成google认证器secretkey
        /// </summary>
        /// <returns></returns>
        public static string GenSecret()
        {
            var secretKey = KeyGeneration.GenerateRandomKey(20);
            return string.Join(secretKey.Select(x=>x.ToString("x2")));
        }

        /// <summary>
        /// 验证google认证器token
        /// </summary>
        /// <param name="user"></param>
        /// <param name="token"></param>
        /// <returns></returns>
        public static bool Verify(string secret,string token)
        {
            var otp = new Totp(secret);
            var timeStepMatched = 0;
            var valid = otp.VerifyTotp(token, out timeStepMatched, new VerificationWindow(2, 2));

            return valid;
        }
    }

- 参考https://www.jerriepelser.com/blog/using-google-authenticator-asp-net-identity