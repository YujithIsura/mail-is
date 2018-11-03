# mail-is

## This repository contains following Docker resources:
- WSO2 Identity Server Dockerfile witch has been linkd with separate openLDAP
- openLDAP Dockerfile
- Docker Compose files to manage those dockerfiles.

The identity server dockerfile build genaric docker images from wso2 is official docker image for deploing identity server in containerized envirenments. also include some configurations to link new openLDAP to WSO2 identity server. The openLDAP dockerfile includes osixia/openldap 1.2.2
       Docker compose file has been created for managing Dockerfiles, passing envirenment variables, etc. This includes phpldapadmin image as well.. for managing new openLDAP.
       
       In here we create new keystore for wso2 is other than using wso2 is default keystore.
       
##  Configuration

1. Create a keystore using a new certificate

    using following command you can create new keystore

 #### keytool -genkey -alias wso2 -keyalg RSA -keystore wso2carbon.jks -keysize 2048
 
2. Change carbon.xml file in /product-is/ldap_dep/ directory
   
    use password when you used to create keystore for <KeyPassword> and use aliias as you used in above command in <KeyAlias>
         
       <KeyStore>
            <!-- Keystore file location-->
            <Location>${carbon.home}/repository/resources/security/wso2carbon.jks</Location>
            <!-- Keystore type (JKS/PKCS12 etc.)-->
            <Type>JKS</Type>
            <!-- Keystore password-->
            <Password>adminlsf</Password>
            <!-- Private Key alias-->
            <KeyAlias>wso2</KeyAlias>
            <!-- Private Key password-->
            <KeyPassword>adminlsf</KeyPassword>
        </KeyStore>

        <InternalKeyStore>
            <!-- Keystore file location-->
            <Location>${carbon.home}/repository/resources/security/wso2carbon.jks</Location>
            <!-- Keystore type (JKS/PKCS12 etc.)-->
            <Type>JKS</Type>
            <!-- Keystore password-->
            <Password>adminlsf</Password>
            <!-- Private Key alias-->
            <KeyAlias>wso2</KeyAlias>
            <!-- Private Key password-->
            <KeyPassword>adminlsf</KeyPassword>
        </InternalKeyStore>
        
 3. Change catalina-server.xml file in
  
