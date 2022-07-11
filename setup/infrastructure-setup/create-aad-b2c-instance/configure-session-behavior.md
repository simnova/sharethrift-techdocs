# Configure Session Behavior

Prerequisites:&#x20;

Complete the steps in [Identity Experience Framework](identity-experience-framework.md)



## Configure the custom policy

1. Open your SignUpOrSignin.xml file
2. Add the following xml between the \<UserJourneyBehaviors> tag after everything else that is already there.

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Application" />
  <SessionExpiryType>Absolute</SessionExpiryType>
  <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

The \<RelyingParty> should look like this after you add the above code.

{% code title="SignUpOrSignin.xml" %}
```xml
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
      <SingleSignOn Scope="Application" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
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
</TrustFrameworkPolicy>xm
```
{% endcode %}
