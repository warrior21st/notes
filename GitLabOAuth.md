## gitlab oauth
##### code
        public IActionResult GitLabOAuth()
        {
            var clientId = "appid";
            var state = "abc123";
            var reUrl = "http://localhost:5000/login/authd/gitlab";
            var url = $"https://gitlab.com/oauth/authorize?client_id={clientId}&&response_type=code&state={state}&redirect_uri={reUrl}&scope=read_user+openid+email;";

            return Redirect(url);
        }

        public IActionResult GitlabAuthed(string state, string code)
        {
            var clientId = "appid";
            var clientSecret = "appsecret";
            var getAccessTokenUrl = $"https://gitlab.com/oauth/token";
            var str = $"client_id={clientId}&client_secret={clientSecret}&code={code}&grant_type=authorization_code&redirect_uri=http://localhost:5000/login/authd/gitlab";
            getAccessTokenUrl += "?" + str;
            var tokenResponse = _httpClient.PostAsync(getAccessTokenUrl, null).Result;

            var res = tokenResponse.Content.ReadAsStringAsync().Result;
            var token = JObject.Parse(res)["access_token"].ToString();
            var userInfoRequest = new HttpRequestMessage();
            userInfoRequest.Method = HttpMethod.Get;
            userInfoRequest.RequestUri = new Uri($"https://gitlab.com/api/v4/user?access_token={token}", UriKind.Absolute);
            var userInfos = _httpClient.SendAsync(userInfoRequest).Result;

            return Content(userInfos.Content.ReadAsStringAsync().Result);
        }
##### response
    {
        "id": 5067590,
        "name": "warriorHuang",
        "username": "warrior21st",
        "state": "active",
        "avatar_url": "https://secure.gravatar.com/avatar/8d0b6ce0cc133f2e433636712aacdb88?s=80&d=identicon",
        "web_url": "https://gitlab.com/warrior21st",
        "created_at": "2019-12-04T03:32:31.573Z",
        "bio": null,
        "location": null,
        "public_email": "",
        "skype": "",
        "linkedin": "",
        "twitter": "",
        "website_url": "",
        "organization": null,
        "last_sign_in_at": "2019-12-04T03:32:31.661Z",
        "confirmed_at": null,
        "last_activity_on": "2019-12-04",
        "email": "123767168@qq.com",
        "theme_id": 1,
        "color_scheme_id": 1,
        "projects_limit": 100000,
        "current_sign_in_at": "2019-12-04T03:32:31.661Z",
        "identities": [],
        "can_create_group": true,
        "can_create_project": true,
        "two_factor_enabled": false,
        "external": false,
        "private_profile": false,
        "shared_runners_minutes_limit": null,
        "extra_shared_runners_minutes_limit": null
    }

- 参照 https://docs.gitlab.com/ee/api/oauth2.html