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

The \<RelyingParty> element should look like this after you add the above code.

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

There are some things you may need to change here to suit your usage.

\
1\) "Scope" defines the granularity. Possible values are: **Suppressed**, **Tenant**, **Application**, and **Policy**

2\) \<SessionExpiryType> is based off of the number you set for \<SessionExpiryInSeconds>.\
**Rolling**: It will keep the user logged in as long as they are performing actions on the application. Countdown starts after the last user action.\
**Absolute**: This option will force the user to reauthenticate after the specified number of seconds have passed.

3\) \<SessionExpiryInSeconds> can take a value including and between 900 seconds (15 minutes) and 86,400 seconds (24 hours).

