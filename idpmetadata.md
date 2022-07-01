# Obtaining Shibboleth IdP metadata

## InCommon provided metadata

If your Service Provider is using InCommon metadata provided either through an XML file or MDQ you are already set. No action is required from you.

## Shibboleth Service Provider(SP)

### Loading metadata from XML file

This is very simple and basic way to load the IdP metadata. In your `shibboleth2.xml` file you need to find `<MetadataProvider/>` element and make it look like the following example
```xml
<MetadataProvider type="XML" path="/path/to/the/metadata.xml"/>
```
where **`path`** value is the actual path to the IdP metadata file. You need to be sure that the **`shibd`** daemon is able to read this file.
You can download Shibboleth IdP metadata from https://shibboleth.nyu.edu/idp/shibboleth.

For additional information how to configure this option please visit [XML Metadata Provider](https://shibboleth.atlassian.net/wiki/spaces/SP3/pages/2063696005/XMLMetadataProvider)

### Loading metadata dynamically

:warning: **_This is recommened way to load IdP metadata_**

Loading IdP metadata dynamically is the preferred way to configure Shibboleth SP which is not InCommon member and this method is very similar to loading the metadata from a file.

Please find `<MetadataProvider/>` element in your `shibboleth2.xml` file and edit it to look like the following example
```xml
<MetadataProvider type="XML"
        url="https://shibboleth.nyu.edu/idp/shibboleth"
        backingFilePath="/path/to/your/backup.xml">
  <MetadataFilter type="Signature" certificate="metadata-signing-key.pem"/>
  <MetadataFilter type="RequireValidUntil" maxValidityInterval="8640000"/>
</MetadataProvider>
```
The **`backingFilePath`** has to be a location where your **`shibd`** has _read_ and _write_ access

For additional information how to configure this option please visit [XML Metadata Provider](https://shibboleth.atlassian.net/wiki/spaces/SP3/pages/2063696005/XMLMetadataProvider)

## Third party SP supporting metadata upload

### Using XML file

You can download Shibboleth IdP metadata from https://shibboleth.nyu.edu/idp/shibboleth and save it to a file which you can upload to your service provider on the day and the time we change the certificates.

### Using dynamic load

If your Service Provider supports dynamic metadata you can put this URL https://shibboleth.nyu.edu/idp/shibboleth and you should be set.

## Third party SP with manual configuration

Service Providers who requires manual configuration need to download in most cases just the IdP's signing certificates. If your Service Provider encrypts authentication requests you will also need IdP's encryption certificate.
We are supportings following services
- Single Sign On using HTTP-POST binding - https://shibboleth.nyu.edu/idp/profile/SAML2/POST/SSO
- Single Sign On using HTTP-Redirect binding - https://shibboleth.nyu.edu/idp/profile/SAML2/Redirect/SSO **(Preferred)**
- Single Logout using HTTP-POST binding - https://shibboleth.nyu.edu/idp/profile/SAML2/POST/SLO
- Single Logout using HTTP-Redirect binding - https://shibboleth.nyu.edu/idp/profile/SAML2/Redirect/SLO

:warning: **_The Single Logout binding method must correspond to Single Sign On binding one._**

The certificate switch has to happen on the day and at the time we change them on the IdP.

You can download the certificates in advance from here
- [Signing certificate]()
- [Encrypting certificate]()


