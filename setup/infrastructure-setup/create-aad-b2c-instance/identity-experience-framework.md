# Identity Experience Framework

Creating Custom Policy



Download the pack from:

{% embed url="https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip" %}

Unzip the file from above,



The file includes the following:

```
/Display Controls Starterpack
LICENSE
/LocalAccounts
  - PasswordReset.xml
  - ProfileEdit.xml
  - SignUpOrSignin.xml             <-- #3 
  - TrustFrameworkBase.xml         <-- #1 
  - TrustFrameworkExtensions.xml   <-- #2
README.md
...
```

Open Each in VS Code:



Add ClaimType, ClaimsProvider



Your tenant would be **sharethriftb2c<\<random number>>.onmicrosoft.com** that you created earlier.

{% code title="TrustFrameworkBase.xml" %}
```markup
...
<TrustFrameworkPolicy
  ...
  TenantId="<<your tenant>>.onmicrosoft.com"
  PublicPolicyUri="http://<<your tenant>>.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
  <BuildingBlocks>
    <ClaimsSchema>
    ...
      <!-- ADD THIS BEGIN: -->
      <ClaimType Id="bodyData">
        <DisplayName>Body Data</DisplayName>
        <DataType>string</DataType>
        <DefaultPartnerClaimTypes>
          <Protocol Name="OAuth2" PartnerClaimType="bodyData" />
          <Protocol Name="OpenIdConnect" PartnerClaimType="bodyData" />
          <Protocol Name="SAML2" PartnerClaimType="http://schemas.microsoft.com/identity/claims/bodyData" />
        </DefaultPartnerClaimTypes>
      </ClaimType>
      <!-- ADD THIS END -->
    </ClaimsSchema>
  ...
    <ContentDefinitions>
      <!-- REPLACE ALL WITH BEGIN: -->
      <!-- This content definition is to render an error page that displays unhandled errors. -->
      <ContentDefinition Id="api.error">
        <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Error page</Item>
        </Metadata>
      </ContentDefinition>

      <ContentDefinition Id="api.idpselections">
        <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Idp selection page</Item>
          <Item Key="language.intro">Sign in</Item>
        </Metadata>
      </ContentDefinition>

      <ContentDefinition Id="api.idpselections.signup">
        <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Idp selection page</Item>
          <Item Key="language.intro">Sign up</Item>
        </Metadata>
      </ContentDefinition>

      <ContentDefinition Id="api.signuporsignin">
        <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>

      <ContentDefinition Id="api.selfasserted">
        <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Collect information from user page</Item>
        </Metadata>
      </ContentDefinition>

      <ContentDefinition Id="api.selfasserted.profileupdate">
        <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Collect information from user page</Item>
        </Metadata>
      </ContentDefinition>

      <ContentDefinition Id="api.localaccountsignup">
        <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Local account sign up page</Item>
        </Metadata>
      </ContentDefinition>

      <ContentDefinition Id="api.localaccountpasswordreset">
        <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Local account change password page</Item>
        </Metadata>
      </ContentDefinition>
      <!-- REPLACE ALL WITH END -->
    </ContentDefinitions>
    ...  
  </BuildingBlocks>
  <ClaimsProviders>
  <!-- ADD THIS BEGIN: -->
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
      <TechnicalProfile Id="REST-SignIn">
      <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://c160c2e8440b.ngrok.io/api/Identity/testpost</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid"/>
        <InputClaim ClaimTypeReferenceId="email" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="surname" />
        <OutputClaim ClaimTypeReferenceId="bodyData" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
      </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  <!-- ADD THIS END -->
  ...
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <!-- ADD THIS BEGIN: -->
        <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Username">
          <DisplayName>Local Account Signin</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="SignUpTarget">SignUpWithLogonEmailExchange</Item>
            <Item Key="setting.operatingMode">Username</Item>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="signInName" Required="true" />
            <OutputClaim ClaimTypeReferenceId="password" Required="true" />
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" />
            <OutputClaim ClaimTypeReferenceId="bodyData" DefaultValue="{}"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
          </ValidationTechnicalProfiles>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
        </TechnicalProfile>
        <!-- ADD THIS END -->
        ...
      <TechnicalProfiles>
    </ClaimsProvider>  
  </ClaimsProviders>
  ...
  <UserJourneys>
    ...
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        ...
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
          <ClaimsExchanges>
            <!-- REPLACE CONTENTS WITH BEGIN: -->
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Username" />
            <!-- REPLACE CONTENTS WITH END -->
          </ClaimsExchanges>
        
        </OrchestrationStep>
        ...
        <!-- REMOVE STEP4 BEGIN: -->
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        <!-- REMOVE STEP4 END -->
        ...
        <!-- ADD THIS BEGIN: -->
        <OrchestrationStep Order="4" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-SignIn" />
          </ClaimsExchanges>
        </OrchestrationStep>
 
        <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        <!-- ADD THIS END -->
</TrustFrameworkPolicy>
```
{% endcode %}

Update the ClaimsProvider/Metadata/Item Key="ServiceURL" to the URL of your service.

Note you can operate without HTTPS by . adding:\


```markup
<Item Key="AllowInsecureAuthInProduction">True</Item>
```

{% code title="SignUpOrSignin.xml" %}
```markup
...
<TrustFrameworkPolicy
  ...
  TenantId="<<your tenant>>.onmicrosoft.com"
  PublicPolicyUri="http://<<your tenant>>.onmicrosoft.com/B2C_1A_signup_signin">
  ...
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    ...
  </BasePolicy>
  ...
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <!-- ADD THIS BEGIN: (Be sure to use your AppInsights Key) -->
    <UserJourneyBehaviors>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-app-insights-key" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ScriptExecution>Allow</ScriptExecution>
    </UserJourneyBehaviors>
    <!-- ADD THIS END -->
    <TechnicalProfile Id="PolicyProfile">
      ...
      <OutputClaims>
        <!-- ADD THIS BEGIN: -->
        <OutputClaim ClaimTypeReferenceId="bodyData" />
        <!-- ADD THIS END -->
      </OutputClaims>
      ...
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
{% endcode %}

{% code title="TrustFrameworkExtensions.xml" %}
```markup
...
<TrustFrameworkPolicy
  ...
  TenantId="<<your tenant>>.onmicrosoft.com"
  PublicPolicyUri="http://<<your tenant>>.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">
  ...
  ...
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    ...
  </BasePolicy>
  ....
  <BuildingBlocks>
    <!-- ADD THIS BEGIN: -->
    <ContentDefinitions>
    
      <ContentDefinition Id="api.error">
        <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Error page</Item>
        </Metadata>
      </ContentDefinition>
        
      <ContentDefinition Id="api.idpselections">
        <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Idp selection page</Item>
          <Item Key="language.intro">Sign in</Item>
        </Metadata>
      </ContentDefinition>
        
      <ContentDefinition Id="api.idpselections.signup">
        <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Idp selection page</Item>
          <Item Key="language.intro">Sign up</Item>
        </Metadata>
      </ContentDefinition>
        
      <ContentDefinition Id="api.signuporsignin">
        <LoadUri>https://sharethrift.blob.core.windows.net/root/login.html</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
        
      <ContentDefinition Id="api.selfasserted">
        <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Collect information from user page</Item>
        </Metadata>
      </ContentDefinition>
        
      <ContentDefinition Id="api.selfasserted.profileupdate">
        <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Collect information from user page</Item>
        </Metadata>
      </ContentDefinition>
        
      <ContentDefinition Id="api.localaccountsignup">
        <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Local account sign up page</Item>
        </Metadata>
      </ContentDefinition>
        
      <ContentDefinition Id="api.localaccountpasswordreset">
        <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Local account change password page</Item>
        </Metadata>
      </ContentDefinition>      
      
    </ContentDefinitions>

    <!-- ADD THIS END -->
  </BuildingBlocks>

  ...
  </TrustFrameworkPolicy>
```
{% endcode %}

Install these in this particular order:

1. Base
2. Extensions
3. SignUpOrSignin



