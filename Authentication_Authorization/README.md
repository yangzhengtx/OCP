For users to interact with OpenShift Container Platform, they must first authenticate to the cluster. The authentication layer identifies the user associated with requests to the OpenShift Container Platform API. The authorization layer then uses information about the requesting user to determine if the request is allowed.

Authentication and authorization are the two security layers responsible for enabling user interaction with the cluster. When a user makes a request to the API, the API associates the user with the request. The authentication layer authenticates the user. Upon successful authentication, the authorization layer decides to either honor or reject the API request. The authorization layer uses role-based access control (RBAC) policies to determine user privileges.

# Authentication
OCP doesn't store user credentials, instead OCP internal OAuth server uses the configured identity provider to determine the identity of the person making the request. Once the user is allowed to login OCP, OAuth server provides access tokens to authenticate users to the API.
- Configuring an identity provider:
- Configuring the internal OAuth Server:


The OpenShift API has two methods for authenticating requests:
- OAuth Access Tokens
  - The API server reads bearer tokens from a file when given the --token-auth-file=SOMEFILE option on the command line. Currently, tokens last indefinitely, and the token list cannot be changed without restarting the API server.
  - When a person requests a new OAuth token, the OAuth server uses the configured identity provider to determine the identity of the person making the request.It then determines what user that identity maps to, creates an access token for that user, and returns the token for use.
  
- X.509 Client Certificates
  - Client certificate authentication is enabled by passing the --client-ca-file=SOMEFILE option to API server. The referenced file must contain one or more certificate authorities to use to validate client certificates presented to the API server. If a client certificate is presented and verified, the common name of the subject is used as the user name for the request. As of Kubernetes 1.4, client certificates can also indicate a user's group memberships using the certificate's organization fields. To include multiple group memberships for a user, include multiple organization fields in the certificate.


The OpenShift Container Platform provides the Authentication operator, which runs an OAuth server. The OAuth server provides OAuth access tokens to users when they attempt to authenticate to the API. An identity provider must be configured and available to the OAuth server. The OAuth server uses an identity provider to validate the identity of the requester. The server reconciles the user with the identity and creates the OAuth access token for the user. OpenShift automatically creates identity and user resources after a successful login.



# Authorization
- RBAC: Rules,  Local/Cluster Roles and Bindings. For user/group/SA.
- SCC for Pod


Reference:
- > https://docs.openshift.com/container-platform/4.10/authentication/index.html
- > https://learnk8s.io/authentication-kubernetes
