## 1. Authentication
### 1.1 Metastore authentication
See `org.apache.hadoop.hive.metastore.AuthFactory#getAuthTransFactory`
```xml
<property>
  <name>hive.metastore.sasl.enabled</name>
  <value>true</value>
  <description>If true, the metastore thrift interface will be secured with SASL framework. Clients must authenticate with Kerberos.</description>
</property>

<property>
  <name>hive.metastore.kerberos.keytab.file</name>
  <value>/etc/hive/conf/hive.keytab</value>
  <description>The sample path to the Kerberos Keytab file containing the metastore thrift server's service principal.
  </description>
</property>

<property>
  <name>hive.metastore.kerberos.principal</name>
  <value>hive/_HOST@YOUR-REALM.COM</value>
  <description>The service principal for metastore server.</description>
</property>
```

### 1.2 HiveServer2 authentication
`hiveserver2` supports multiple authentication modes, such as Kerberos, LDAP, PAM, and customized code.  
See `org.apache.hive.service.auth.HiveAuthFactory#getAuthTransFactory`

#### 1.2.1 `NONE`
```xml
<property>
  <name>hive.server2.authentication</name>
  <value>NONE</value>
</property>
```

#### 1.2.2 `KERBEROS`
```xml
<property>
  <name>hive.server2.authentication</name>
  <value>KERBEROS</value>
</property>

<property>
  <name>hive.server2.authentication.kerberos.keytab</name>
  <value>/etc/hive/conf/hive.keytab</value>
</property>

<property>
  <name>hive.server2.authentication.kerberos.principal</name>
  <value>hive/_HOST@YOUR-REALM.COM</value>
</property>
```

#### 1.2.3 `LDAP`
```xml
<property>
  <name>hive.server2.authentication</name>
  <value>LDAP</value>
</property>

<property>
  <name>hive.server2.authentication.ldap.url</name>
  <value>LDAP_URL, such as ldap://ldaphost@company.com</value>
</property>

<property>
  <name>hive.server2.authentication.ldap.Domain</name>
  <value>Domain Name</value>
</property>

<property>
  <name>hive.server2.authentication.ldap.baseDN</name>
  <value>LDAP_BaseDN, such as ou=people,dc=packtpub,dc=com</value>
</property>
```

#### 1.2.4 `PAM`
```xml
<property>
  <name>hive.server2.authentication</name>
  <value>PAM</value>
</property>

<property>
  <name>hive.server2.authentication.pam.services</name>
  <value>pluggable-auth-class-name</value>
  <description>Set this to a list of comma-separated PAM servicesthat will be used. Note that a file with the name as the PAMservice must exist in /etc/pam.d.</description>
</property>
```

#### 1.2.4 `CUSTOM`
```xml
<property>
  <name>hive.server2.authentication</name>
  <value>CUSTOM</value>
</property>

<property>
  <name>hive.server2.custom.authentication.class</name>
  <value>pluggable-auth-class-name</value>
  <description>Customized authentication class name, such as com.packtpub.hive.essentials.hiveudf.</description>
</property>
```

## 2. Authorization
### 2.1 Legacy mode
```xml
<property>
  <name>hive.security.authorization.enabled</name>
  <value>true</value>
  <description>enables or disable the hive client authorization</description>
</property>

<property>
  <name>hive.security.authorization.createtable.owner.grants</name>
  <value>ALL</value>
  <description>The privileges automatically granted to the owner whenever a table gets created. An example like "select, drop" will grant select and drop privilege to the owner of the table.</description>
</property>
```

### 2.2 Storage-based mode
```xml
<property>
  <name>hive.security.authorization.enabled</name>
  <value>true</value>
  <description>enable or disable the hive client authorization</description>
</property>

<property>
  <name>hive.metastore.pre.event.listeners</name>
  <value>org.apache.hadoop.hive.ql.security.authorization.AuthorizationPreEventListener</value>
  <description>This turns on metastore-side security.</description>
</property>

<property>
  <name>hive.security.authorization.manager</name>
  <value>org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider</value>
  <description>The class name of the Hive client AuthorizationManager.</description>
</property>

<property>
  <name>hive.security.metastore.authorization.manager</name>
  <value>org.apache.hadoop.hive.ql.security.HadoopDefaultMetastoreAuthenticator</value>
  <description>authenticator manager class name to be used in the metastore for authentication.</description>
</property>

<property>
  <name>hive.security.metastore.authorization.auth.reads</name>
  <value>true</value>
  <description>If this is true, metastore authorizer authorizes read actions on database, table</description>
</property>
```

### 2.3 SQL standard-based mode
```xml
<!-- hive-site.xml -->
<property>
  <name>hive.security.authorization.enabled</name>
  <value>true</value>
  <description>enable or disable the hive client authorization </description>
</property>

<property>
  <name>hive.server2.enable.doAs</name>
  <value>false</value>
  <description>Allows Hive queries to be run by the user who submits the query rather than the hive user. Need to turn if off for this SQL standard-base mode</description>
</property>

<property>
  <name>hive.users.in.admin.role</name>
  <value>dayongd,administrator</value>
  <description>Comma-separated list of users assigned to the ADMIN role.</description>
</property>

<property>
  <name>hive.security.authorization.manager</name>
  <value>org.apache.hadoop.hive.ql.security.authorization.plugin.sql</value>
</property>

<property>
  <name>hive.security.authenticator.manager</name>
  <value>org.apache.hadoop.hive.ql.security.authorization.plugin.sqlstd.SQLStdConfOnlyAuthorizerFactory</value>
</property>

<property>
  <name>hive.security.metastore.authorization.manager</name>
  <value>
    org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider,
    org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly
  </value>
  <description>It takes a comma separated list, so we can add MetaStoreAuthzAPIAuthorizerEmbedOnly along with StorageBasedAuthorization parameter,if we want to enable that as well</description>
</property>
```

```xml
<!-- hiveserver2-site.xml -->
<property>
  <name>hive.security.authorization.enabled</name>
  <value>true</value>
</property>

<property>
  <name>hive.security.authorization.manager</name>
  <value>org.apache.hadoop.hive.ql.security.authorization.plugin.sqlstd.SQLStdHiveAuthorizerFactory</value>
</property>

<property>
  <name>hive.security.authenticator.manager</name>
  <value>org.apache.hadoop.hive.ql.security.SessionStateUserAuthenticator</value>
</property>

<property>
  <name>hive.metastore.uris</name>
  <value>thrift://localhost:9085</value>
  <description>Use 9083 for hive1 and 9085 for hive2</description>
</property>
```
