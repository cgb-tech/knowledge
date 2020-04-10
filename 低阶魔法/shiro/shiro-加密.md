shiro中的加密

常用的加密方式(shiro)有两种`MD5`和`SHA`,在进行散列的时候最好提供一个salt(盐) 

```java
@Test
public void textMD5(){
    String username = "lkr";
    String id = "123";
    //4d72656afcf5ee5819bbf5cf6e973f38
    Md5Hash md5Hash = new Md5Hash( username , id ,3);
    //f666dc9480768a5ec14c139433c700d9
    System.out.println(md5Hash);
}
```

在使用到加密验证的时候,还会使用到这里面的方法,这是其中一个方法的参数和介绍

```java
/**
     * Constructor that takes in a single 'primary' principal of the account, its corresponding hashed credentials,
     * the salt used to hash the credentials, and the name of the realm to associate with the principals.
     * <p/>
     * This is a convenience constructor and will construct a {@link PrincipalCollection PrincipalCollection} based
     * on the <code>principal</code> and <code>realmName</code> argument.
     *
     * @param principal         the 'primary' principal associated with the specified realm.
     * @param hashedCredentials the hashed credentials that verify the given principal.
     * @param credentialsSalt   the salt used when hashing the given hashedCredentials
     * @param realmName         the realm from where the principal and credentials were acquired.
     * @see org.apache.shiro.authc.credential.HashedCredentialsMatcher HashedCredentialsMatcher
     * @since 1.1
     */
public SimpleAuthenticationInfo(Object principal, Object hashedCredentials, ByteSource credentialsSalt, String realmName) {
    this.principals = new SimplePrincipalCollection(principal, realmName);
    this.credentials = hashedCredentials;
    this.credentialsSalt = credentialsSalt;
}

```

