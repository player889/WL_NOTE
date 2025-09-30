- Spring Oauth 2.0 logout 標籤裡的logout-success-url和success-handler-ref只能指定其中一個, 不能兩個都指定.
- ClientRegistration - 表示OAuth 2.0或OpenID Connect 1.0客戶端註冊的信息 。
	客戶端註冊包含客戶端ID，客戶端密碼，授權授權類型，重定向URI，範圍，授權URI，令牌URI和其他詳細信息。
- RedirectUriTemplate - 客戶端重定向URI，授權服務器將最終用戶的用戶代理重定向到最終用戶對客戶端進行身份驗證和授權訪問之後。默認的重定向URI模板是{baseUrl}/login/oauth2/code/{registrationId}，它支持URI模板變量。
- authorizationUri - 授權服務器的授權端點URI。
- (userInfoEndpoint)uri - UserInfo端點URI，用於訪問經過身份驗證的最終用戶的聲明/屬性。
- ClientRegistrationRepository 用作於OAuth 2.0 / OpenID Connect 1.0 的 ClientRegistration存儲。
- ClientRegistrationRepository的默認實現是InMemoryClientRegistrationRepository。
- Spring Boot 2.0自動配置還會在ApplicationContext註冊ClientRegistrationRepository作為@Bean，這樣它可用於依賴註入。

	```java
	@Controller
	public class MainController {
	
	    @Autowired
	    private ClientRegistrationRepository clientRegistrationRepository;
	
	    @RequestMapping("/")
	    public String index() {
	        ClientRegistration googleRegistration =
	            this.clientRegistrationRepository.findByRegistrationId("google");
	        ...
	        return "index";
	    }
	}
	```

- CommonOAuth2Provider為眾多知名供應商預先定義了一組默認的客戶端屬性：Google，GitHub，Facebook和Okta。例如，authorization-uri，token-uri，和user-info-uri不經常對供應商變更。因此，為了減少所需的配置，提供默認值是有意義的。如前所述，當我們配置一個GitHub客戶端時，只需要client-id和client-secret屬性。

	```yaml
	spring:
	  security:
	    oauth2:
	      client:
	        registration:
	          github:
	            client-id: github-client-id
	            client-secret: github-client-secret
	```

- OAuth2AuthorizedClientService主要的作用是管理OAuth2AuthorizedClient實例。

- OAuth2AuthorizedClient表示授權客戶端。客戶端授權時被認為是最終用戶(資源所有者)已經授予授權客戶端訪問受保護的資源。

	```java
	@Controller
	public class MainController {
	
	    @Autowired
	    private OAuth2AuthorizedClientService authorizedClientService;
	
	    @RequestMapping("/userinfo")
	    public String userinfo(OAuth2AuthenticationToken authentication) {
	        // authentication.getAuthorizedClientRegistrationId() returns the
	        // registrationId of the Client that was authorized during the Login flow
	        OAuth2AuthorizedClient authorizedClient =
	            this.authorizedClientService.loadAuthorizedClient(
	                authentication.getAuthorizedClientRegistrationId(),
	                authentication.getName());
	
	        OAuth2AccessToken accessToken = authorizedClient.getAccessToken();
	        ...
	        return "userinfo";
	    }
	}
	```

- Spring Boot 2.0自動配置將每個下面的屬性綁定 到一個實例上，然後編寫一個實例中的每個實例。

	| **Spring Boot 2.0**                                          | ClientRegistration                                     |
	| :----------------------------------------------------------- | :----------------------------------------------------- |
	| spring.security.oauth2.client.registration.[registrationId]  | registrationId                                         |
	| spring.security.oauth2.client.registration.[registrationId].client-id | clientId                                               |
	| spring.security.oauth2.client.registration.[registrationId].client-secret | clientSecret                                           |
	| spring.security.oauth2.client.registration.[registrationId].client-authentication-method | clientAuthenticationMethod                             |
	| spring.security.oauth2.client.registration.[registrationId].authorization-grant-type | authorizationGrantType                                 |
	| spring.security.oauth2.client.registration.[registrationId].redirect-uri-template | redirectUriTemplate                                    |
	| spring.security.oauth2.client.registration.[registrationId].scope | scopes                                                 |
	| spring.security.oauth2.client.registration.[registrationId].client-name | clientName                                             |
	| spring.security.oauth2.client.provider.[providerId].authorization-uri | providerDetails.authorizationUri                       |
	| spring.security.oauth2.client.provider.[providerId].token-uri | providerDetails.tokenUri                               |
	| spring.security.oauth2.client.provider.[providerId].jwk-set-uri | providerDetails.jwkSetUri                              |
	| spring.security.oauth2.client.provider.[providerId].user-info-uri | providerDetails.userInfoEndpoint.uri                   |
	| spring.security.oauth2.client.provider.[providerId].userNameAttribute | providerDetails.userInfoEndpoint.userNameAttributeName |

