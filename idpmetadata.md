## Obtaining Shibboleth IdP metadata

?> Any questions related to the Identity Provider metadata not answered below can be answered during the [_IAM Office Hours - SSO and MFA_](https://calendar.google.com/calendar/embed?src=nyu.edu_23qne9rsrhuidta3ld7pnkok9o%40group.calendar.google.com&ctz=America%2FNew_York)

### InCommon provided metadata

If your Service Provider is using InCommon metadata provided either through an XML file or an [MDQ (Metadata Query) Service](https://shibboleth.atlassian.net/wiki/spaces/SP3/pages/2060616133/MDQMetadataProvider) you are already set. No action is required from you since you have the metadata file and that's what is needed.

### Shibboleth Service Provider(SP)

#### Loading metadata from XML file

This is a simple and basic way to load the IdP metadata. In your `shibboleth2.xml` file you need to find `<MetadataProvider/>` element and make it look like the following example:
```xml
<MetadataProvider type="XML" path="/path/to/the/metadata.xml"/>
```
where **`path`** value is the actual path to the IdP metadata file. You need to be sure that the **`shibd`** daemon is able to **_read_** this file.

You can download NYU's Shibboleth IdP metadata from https://shibboleth.nyu.edu/idp/shibboleth.

For additional information how to configure this option please visit [XML Metadata Provider](https://shibboleth.atlassian.net/wiki/spaces/SP3/pages/2063696005/XMLMetadataProvider)

#### Loading metadata dynamically

?> **_This is the recommended way to load IdP metadata_**

Loading IdP metadata dynamically is the preferred way to configure a Shibboleth SP which is not an InCommon member.  This method is very similar to loading the metadata from a file.

Please find the `<MetadataProvider/>` element in your `shibboleth2.xml` file and edit it to look like the following example:
```xml
<MetadataProvider type="XML"
        url="https://shibboleth.nyu.edu/idp/shibboleth"
        backingFilePath="/path/to/your/backup.xml"
        maxRefreshDelay="PT1H">
</MetadataProvider>
```
The **`backingFilePath`** has to be a location where your **`shibd`** has **_read_** and **_write_** access. The **`maxRefreshDelay`** defines how ofthen the metadata has to be pulled from the IdP.

?> **`maxRefreshDelay="PT4H"`** will pull metadata every 4 hours

For additional information how to configure this option please visit [XML Metadata Provider](https://shibboleth.atlassian.net/wiki/spaces/SP3/pages/2063696005/XMLMetadataProvider)

### Third party SP supporting metadata upload
#### Using XML file

You can download NYU's Shibboleth IdP metadata from https://shibboleth.nyu.edu/idp/shibboleth and save it to a file which you can upload to your SP (service provider) **before** the date and time of the actual change.

#### Using dynamic load

If your Service Provider supports dynamic metadata you can use this URL https://shibboleth.nyu.edu/idp/shibboleth and you should be set.

### Third party SP with manual configuration

With Service Providers that require manual configuration, you need to download the new certificate from here:  
- PEM formt: [New Production Certificate](https://shibboleth.nyu.edu/prodidp.pem)
- DER formt: [New Production Certificate](https://shibboleth.nyu.edu/prodidp.der)

We support the following services:
- Single Sign On using HTTP-POST binding - https://shibboleth.nyu.edu/idp/profile/SAML2/POST/SSO
- Single Sign On using HTTP-Redirect binding - https://shibboleth.nyu.edu/idp/profile/SAML2/Redirect/SSO **(Preferred)**
- Single Logout using HTTP-POST binding - https://shibboleth.nyu.edu/idp/profile/SAML2/POST/SLO
- Single Logout using HTTP-Redirect binding - https://shibboleth.nyu.edu/idp/profile/SAML2/Redirect/SLO

:warning: **_The Single Logout binding method must correspond to Single Sign On binding._**

!> **The certificate switch has to happen on the day and at the time we change them on the IdP, which is scheduled for August 5th at 16:00 EDT**


---
#### Note - Difference Between Shibboleth vs Third Party

**_Shibboleth Service Provider (SP)_** is

- Software that implements SAML2 which was created by Shibboleth

**_Third party SP (Service Provider)_** is

- Software which implements SAML2 using a library or framework like PySAML2, SimpleSAMLphp, Spring Security SAML, etc.
- Ready to use software like ADFS, Tivoli, Oracle, Ping Idenity, Auth0, Okta, etc.


## Testing new Shibboleth IdP certificate

In case you can update your metadata or upload the new certificate ahead of the change on August 5th at 16:00 EDT, you can test the new setup following the instructions below.

- Connect to NYU VPN using split tunnel or connect to NYU VPN SEC, using AnyConnect, OpenConnect or any other VPM software.
- Save the contents of your host file for the purpose of reverting it back after testing is completed
- You need to edit the host file on your testing computer to point to stage.shibboleth.it.nyu.edu IPs in AWS. Your permission on the testing machine needs to be root for (Linux/OS X) or administrator for (Windows).  
- Paths to the host file based on OS
  - Windows - ```c:\windows\system32\drivers\etc\hosts```
  - Linux - ```/etc/hosts```
  - OS X - ```/etc/hosts```
- Run the following command for (Windows, OS X, Linux)
```bash 
nslookup stage.shibboleth.it.nyu.edu 
```
__Sample response__
```bash
Server:		128.122.0.11
Address:	128.122.0.11#53

stage.shibboleth.it.nyu.edu	canonical name = stage.shibboleth.split.nyu.edu.
stage.shibboleth.split.nyu.edu	canonical name = internal-shibboleth-stage-lb-internal-96999622.us-east-1.elb.amazonaws.com.
Name:	internal-shibboleth-stage-lb-internal-96999622.us-east-1.elb.amazonaws.com
Address: 10.129.104.243
Name:	internal-shibboleth-stage-lb-internal-96999622.us-east-1.elb.amazonaws.com
Address: 10.129.35.116
```

- Run the following command for (OS X, Linux)

```bash
dig +noall +answe shibboleth.it.nyu.edu
```

__Sample Response__

```bash
stage.shibboleth.it.nyu.edu. 86400 IN	CNAME	stage.shibboleth.split.nyu.edu.
stage.shibboleth.split.nyu.edu.	83174 IN CNAME	internal-shibboleth-stage-lb-internal-96999622.us-east-1.elb.amazonaws.com.
internal-shibboleth-stage-lb-internal-96999622.us-east-1.elb.amazonaws.com. 60 IN A 10.129.104.243
internal-shibboleth-stage-lb-internal-96999622.us-east-1.elb.amazonaws.com. 60 IN A 10.129.35.116
```

- Copy the returned IPs to your hosts file and save it. (AWS IPs can change and thus you need to find the current IPs before you test)

```bash
#Stage Shibboleth IdP
10.129.104.243 shibboleth.nyu.edu
10.129.35.116 shibboleth.nyu.edu
```

- Open Incognito/Private/InPrivate window and login to your application
- Test functionality of the application
- Report issues in the Testing Issues sheet
  
!> **You have to revert all changes to your original `hosts` file after you finish the testing!**

