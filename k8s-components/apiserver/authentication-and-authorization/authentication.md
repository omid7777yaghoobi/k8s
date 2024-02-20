
# Supported Authenticatin Plugins:

- ## Static Token File

    to use this plugin all you need to do is to create a file like this:
    ```
    token1,arthur,1,"admin,dev,qa"
    token2,daniele,2,dev
    token3,errge,3,qa
    ```

    The file format is token, user, uid, and groups.
    Then pass this file to your apiserver with --token-auth-file flag.
    
    Disadvantages:
        
    1. You need to know the name of all your users in advance.
    2. Editing the CSV file requires restarting the API server.
    3. Tokens do not expire.

- ## X.509 Certificates

    To use this method you should create a CA file and then pass it's address to the apiserver with --client-ca-file=FILE flag. then you can create your own certificate and then sign it using the kubectl command. if the authorization is already configured you will be good to go and you can get access to your cluster using this certificate. The kube-apiserver verifies the client certificate against the root CA. Then, it proceeds to extract the username and group. Kubeadm and OpenShift use this method by default.

    using this method you can have all of these:
    1. You can set an expiry date for the certificates.
    2. Creating new certificates doesn't require changing the flags of the API server.
    3. There is no CSV file. Certificates are managed as Custom Resource Definitions.

    but also there are some disadvantages:
    1. X.509 client certificates are usually long-lived (i.e. years).
    2. The CA infrastructure provides a way to revoke a certificate, but Kubernetes does not support checking for revocation.
    3. Since client certificates are self-contained, they make it very hard to use groups with RBAC.
    4. For your client to authenticate, it must have a point-to-point connection with the API server. This means no reverse proxies or web application firewalls in front of your API server. 
- ## Open ID Connect

    If you already have an OIDC (Open ID Connect) infrastructure where you manage your users then you will be able to use this method as a means of authentication for your cluster.

    Open ID Connect providers issue JSON Web Token (JWT). That means they can be verified autonomously, without contacting the token's issuer, and they also expire.
- ## Authentication Proxy

    The Authenticating Proxy authentication plugin allows users to authenticate to Kubernetes through an external authenticating proxy transparently.

    When users make a request to the Kubernetes cluster, the request is first intercepted by the authenticating proxy.

    This authentication plugin is helpful if you already use an authenticating proxy in your organisation or if you want to implement a custom authentication method that is not supported by any of the other authentication plugins — this is because the authenticating proxy can implement any authentication method you like.
- ## Webhook

    And finally, the Webhook Tokens authentication plugin allows users to authenticate to Kubernetes with an HTTP bearer token that is verified by an external custom authentication service.

    The Webhook Token Authentication plugin is helpful if you want to implement a custom authentication method that is not provided by any other authentication plugins.
