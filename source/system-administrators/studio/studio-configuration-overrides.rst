:is-up-to-date: True
:last-updated: 4.0.1

:orphan:

.. index:: Studio's Configuration Overrides, Configuration Overrides, Overrides

.. _studio-config-override:

================================
Studio's Configuration Overrides
================================

Crafter Studio comes with pre-configured settings that you may want to override.  To view the pre-configured settings in Crafter Studio, in your Authoring installation, go to ``CRAFTER_HOME/bin/apache-tomcat/webapps/studio/WEB-INF/classes/crafter/studio`` and open the file ``studio-config.yaml``.

To override any of the pre-configured settings, in your Authoring installation, go to ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension`` and add the settings you would like to configure in the file ``studio-config-override.yaml``.   The override file have some settings already listed that you may want to override in Crafter Studio:

--------------------------------
Content Repository Configuration
--------------------------------

The following section of Studio's configuration overrides allows you to do the following:

* ``studio.repo.basePath`` allows you to set your repository base
* ``studio.repo.siteSandboxBranch`` allows you to set the default branch to be used by sandbox
* ``studio.repo.published.live`` and ``studio.repo.published.staging`` allows you to set the branch for your publishing targets

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ##################################################
   ##              Content Repository              ##
   ##################################################
   # Absolute or relative path to repository base (all actual repositories will be under this)
   studio.repo.basePath: ../data/repos
   # Sandbox Git repository branch for every site
   # studio.repo.siteSandboxBranch: master
   # Git repository branch for publishing targets are configured here
   # Git repository branch for the `live` publishing target, default "live"
   # studio.repo.published.live: live
   # Git repository branch for the `staging` publishing target, default "staging"
   # studio.repo.published.staging: staging

---------------------
Project Configuration
---------------------

The following section of Studio's configuration overrides allows you to setup your project configuration

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ############################################################
   ##                   Site Configuration                   ##
   ############################################################
   # Destroy site context url for preview engine
   studio.configuration.site.preview.destroy.context.url: ${env:ENGINE_URL}/api/1/site/context/destroy.json?crafterSite={siteName}&token=${studio.configuration.management.previewAuthorizationToken}
   # Default preview URL
   studio.configuration.site.defaultPreviewUrl: ^https?://localhost:8080/?
   # Default authoring URL
   studio.configuration.site.defaultAuthoringUrl: ^https?://localhost:8080/studio/?
   # Default GraphQL server URL
   studio.configuration.site.defaultGraphqlServerUrl: ^https?://localhost:8080/?
   # Studio management authorization token.
   studio.configuration.management.authorizationToken: ${env:STUDIO_MANAGEMENT_TOKEN}
   # Preview engine management authorization token.
   studio.configuration.management.previewAuthorizationToken: ${env:ENGINE_MANAGEMENT_TOKEN}
   # Protected URLs with preview engine management authorization token.
   # Coma separated list of preview engine urls
   studio.configuration.management.previewProtectedUrls: >-
     /api/1/monitoring/log.json,
     /api/1/monitoring/memory.json,
     /api/1/monitoring/status.json,
     /api/1/monitoring/version.json,
     /api/1/site/context/id,
     /api/1/site/context/destroy,
     /api/1/site/context/rebuild,
     /api/1/site/context/graphql/rebuild,
     /api/1/site/cache/clear,
     /api/1/site/cache/statistics

------------------------------
Preview Deployer Configuration
------------------------------

The following section of Studio's configuration overrides allows you to setup your deployer urls

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ############################################################
   ##                    Preview Deployer                    ##
   ############################################################

   # Default preview deployer URL (can be overridden per site)
   studio.preview.defaultPreviewDeployerUrl: ${env:DEPLOYER_URL}/api/1/target/deploy/{siteEnv}/{siteName}
   # Default preview create target URL (can be overridden per site)
   studio.preview.createTargetUrl: ${env:DEPLOYER_URL}/api/1/target/create_if_not_exists
   # Default preview create target URL (can be overridden per site)
   studio.preview.deleteTargetUrl: ${env:DEPLOYER_URL}/api/1/target/delete-if-exists/{siteEnv}/{siteName}
   # URL to the preview repository (aka Sandbox) where authors save work-in-progress
   studio.preview.repoUrl: ${env:CRAFTER_DATA_DIR}/repos/sites/{siteName}/sandbox



----------------------------
Preview Search Configuration
----------------------------

The following section of Studio's configuration overrides allows you to setup urls for search in preview

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ############################################################
   ##                   Preview Search                       ##
   ############################################################

   studio.preview.search.createUrl: ${env:SEARCH_URL}/api/2/admin/index/create
   studio.preview.search.deleteUrl: ${env:SEARCH_URL}/api/2/admin/index/delete/{siteName}

----------------------
Database Configuration
----------------------

The following section of Studio's configuration overrides allows you to setup the database url, port number, connection string to initialize the database and path

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ##################################################
   ##                   Database                   ##
   ##################################################

   # Crafter Studio uses an embedded MariaDB by default
   # Crafter DB schema name
   studio.db.schema: ${env:MARIADB_SCHEMA}
   # Crafter DB connection string
   studio.db.url: jdbc:mariadb://${env:MARIADB_HOST}:${env:MARIADB_PORT}/crafter?user=${env:MARIADB_USER}&password=${env:MARIADB_PASSWD}
   # Connection string used to initialize database. This creates the `crafter` schema, the `crafter` user and/or upgrades the database
   studio.db.initializer.url: jdbc:mariadb://${env:MARIADB_HOST}:${env:MARIADB_PORT}?user=${env:MARIADB_ROOT_USER}&password=${env:MARIADB_ROOT_PASSWD}
   # Connection string if using a database with an already created schema and user (like AWS RDS)
   # studio.db.initializer.url: ${studio.db.url}
   # Port number for the embedded database (note this must match what's in the connection URLs in this config file)
   studio.db.port: ${env:MARIADB_PORT}
   # Data folder for the embedded database
   studio.db.dataPath: ${env:MARIADB_DATA_DIR}
   # Socket path for the embedded database
   studio.db.socket: /tmp/MariaDB4j.${env:MARIADB_PORT}.sock

----------------------
Security Configuration
----------------------

The following section of Studio's configuration overrides allows you to randomize the admin password on a fresh install (for more information, see: :ref:`randomize-admin-password`), configure encryption and configure authentication method to be used (for more information, see: :ref:`configuring-studio-security`), configure password requirements validation (for more information see: :ref:`crafter-studio-configure-password-requirements`).

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ##################################################
   ##                   Security                   ##
   ##################################################
   # Enable random admin password generation
   # studio.db.initializer.randomAdminPassword.enabled: false
   # Random admin password length
   # studio.db.initializer.randomAdminPassword.length: 16
   # Random admin password allowed chars
   # studio.db.initializer.randomAdminPassword.chars: ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*_=+-/
   # Time in minutes after which active users will be required to login again
   # studio.security.sessionTimeout: 480
   # Time in minutes after which inactive users will be required to login again
   # studio.security.inactivityTimeout: 30
   #
   # Salt for encrypting
   studio.security.cipher.salt: ${env:CRAFTER_SYSTEM_ENCRYPTION_SALT}
   # Key for encrypting
   studio.security.cipher.key: ${env:CRAFTER_SYSTEM_ENCRYPTION_KEY}
   # Password requirements validation regular expression
   # The supported capture group keys are:
   #   hasNumbers
   #   hasLowercase
   #   hasUppercase
   #   hasSpecialChars
   #   noSpaces
   #   minLength
   #   maxLength
   #   minMaxLength
   # studio.security.passwordRequirements.validationRegex: ^(?=(?<hasNumbers>.*[0-9]))(?=(?<hasLowercase>.*[a-z]))(?=(?<hasUppercase>.*[A-Z]))(?=(?<hasSpecialChars>.*[~|!`,;\/@#$%^&+=]))(?<minLength>.{8,})$

   # The key used for encryption of configuration properties
   studio.security.encryption.key: ${env:CRAFTER_ENCRYPTION_KEY}
   # The salt used for encryption of configuration properties
   studio.security.encryption.salt: ${env:CRAFTER_ENCRYPTION_SALT}

   # The path of the folder used for the SSH configuration
   studio.security.ssh.config: ${env:CRAFTER_SSH_CONFIG}

   # Defines name used for environment specific configuration. It is used for environment overrides in studio. Default value is default.
   studio.configuration.environment.active: ${env:CRAFTER_ENVIRONMENT}


------------------
Mail Configuration
------------------

The following section of Studio's configuration overrides allows you to setup the SMTP server to be used by CrafterCMS when sending emails

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ##################################################
   ##        SMTP Configuration (Email)            ##
   ##################################################

   # Default value for from header when sending emails.
   # studio.mail.from.default: admin@example.com
   # SMTP server name to send emails.
   studio.mail.host: ${env:MAIL_HOST}
   # SMTP port number to send emails.
   studio.mail.port: ${env:MAIL_PORT}
   # SMTP username for authenticated access when sending emails.
   # studio.mail.username:
   # SMTP password for authenticated access when sending emails.
   # studio.mail.password:
   # Turn on/off (value true/false) SMTP authenaticated access protocol.
   # studio.mail.smtp.auth: false
   # Enable/disable (value true/false) SMTP TLS protocol when sending emails.
   # studio.mail.smtp.starttls.enable: false
   # Enable/disable (value true/false) SMTP EHLO protocol when sending emails.
   # studio.mail.smtp.ehlo: true
   # Enable/disable (value true/false) debug mode for email service. Enabling debug mode allows tracking/debugging communication between email service and SMTP server.
   # studio.mail.debug: false


.. _studio-config-override-cors:

----
CORS
----

The following section of Studio's configuration overrides allows you to setup CORS

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:
   :emphasize-lines: 10

   ################################################################
   ##                             CORS                           ##
   ################################################################
   # This is configured as permissive by default for ease of deployment
   # Remember to tighten this up for production

   # Disable CORS headers completely
   # studio.cors.disable: false
   # Value for the Access-Control-Allow-Origin header
   # studio.cors.origins: '*'
   # Value for the Access-Control-Allow-Headers header
   # studio.cors.headers: '*'
   # Value for the Access-Control-Allow-Methods header
   # studio.cors.methods: '*'
   # Value for the Access-Control-Allow-Credentials header
   # studio.cors.credentials: true
   # Value for the Access-Control-Max-Age header
   # studio.cors.maxage: -1

The CORS origins accepts regex patterns.  Values are split using ``,``.  Remember that commas inside
patterns need to be escaped with a ``\`` like:
``studio.cors.origins: 'http://localhost:[8000\,3000],http://*.other.domain'``

------
Search
------

The following section of Studio's configuration overrides allows you to setup the url for search

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ################################################################
   ##                           Search                           ##
   ################################################################
   # URLs to connect to Elasticsearch
   studio.search.urls: ${env:ES_URL}
   # The username for Elasticsearch
   studio.search.username: ${env:ES_USERNAME}
   # The password for Elasticsearch
   studio.search.password: ${env:ES_PASSWORD}
   # The connection timeout in milliseconds, if set to -1 the default will be used
   studio.search.timeout.connect: -1
   # The socket timeout in milliseconds, if set to -1 the default will be used
   studio.search.timeout.socket: -1
   # The number of threads to use, if set to -1 the default will be used
   studio.search.threads: -1
   # Indicates if keep alive should be enabled for sockets used by the search client, defaults to false
   studio.search.keepAlive: false

-------------------
Serverless Delivery
-------------------

The following section of Studio's configuration overrides allows you to setup serverless delivery

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ##########################################################
   ##                 Serverless Delivery                  ##
   ##########################################################
   # Indicates if serverless delivery is enabled
   # studio.serverless.delivery.enabled: false
   # The URL for the serverless delivery deployer create URL
   # studio.serverless.delivery.deployer.target.createUrl: ${studio.preview.createTargetUrl}
   # The URL for the serverless delivery deployer delete URL
   # studio.serverless.delivery.deployer.target.deleteUrl: ${studio.preview.deleteTargetUrl}
   # The template name for serverless deployer targets
   # studio.serverless.delivery.deployer.target.template: aws-cloudformed-s3
   # Replace existing target configuration if one exists?
   # studio.serverless.delivery.deployer.target.replace: false
   # The URL the deployer will use to clone/pull the site's published repo. When the deployer is in a separate node
   # (because of clustering), this URL should be an SSH/HTTP URL to the load balancer in front of the Studios
   # studio.serverless.delivery.deployer.target.remoteRepoUrl: ${env:CRAFTER_DATA_DIR}/repos/sites/{siteName}/published
   # The deployer's local path where it will store the clone of the published site. This property is not needed if
   # the deployer is not the preview deployer, so you can leave an empty string ('') instead
   # studio.serverless.delivery.deployer.target.localRepoPath: ${env:CRAFTER_DATA_DIR}/repos/aws/{siteName}
   # Parameters for the target template. Please check the deployer template documentation for the possible parameters.
   # The following parameters will be sent automatically, and you don't need to specify them: env, site_name, replace,
   # disable_deploy_cron, local_repo_path, repo_url, use_crafter_search
   # studio.serverless.delivery.deployer.target.template.params:
   #   # The delivery Elasticsearch endpoint (optional is authoring is using the same one, specified in the ES_URL env variable)
   #   elastic_search_url:
   #   aws:
   #     # AWS region (optional if specified through default AWS chain)
   #     region: us-east-1
   #     # AWS access key (optional if specified through default AWS chain)
   #     default_access_key:
   #     # AWS secret key (optional if specified through default AWS chain)
   #     default_secret_key:
   #     cloudformation:
   #       # AWS access key (optional if aws.accessKey is specified)
   #       access_key:
   #       # AWS secret key (optional if aws.secretKey is specified)
   #       secret_key:
   #       # Namespace to use for CloudFormation resources (required when target template is aws-cloudformed-s3)
   #       namespace: myorganization
   #       # The domain name of the serverless delivery LB (required when target template is aws-cloudformed-s3)
   #       deliveryLBDomainName:
   #       # The SSL certificate ARN the CloudFront CDN should use (optional when target template is aws-cloudformed-s3)
   #       cloudfrontCertificateArn:
   #       # The alternate domains names (besides *.cloudfront.net) for the CloudFront CDN (optional when target template is aws-cloudformed-s3)
   #       alternateCloudFrontDomainNames:

-----------------
Forwarded Headers
-----------------

The following section of Studio's configuration overrides allows you to configure forwarded headers to resolve the actual hostname and protocol when it is behind a load balancer or reverse proxy.

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

       ##################################################
       ##             Forwarded Headers                ##
       ##################################################
       # Indicates if Forwarded or X-Forwarded headers should be used when resolving the client-originated protocol and
       # address. Enable when Studio is behind a reverse proxy or load balancer that sends these
       studio.forwarded.headers.enabled: false

-------------
Access Tokens
-------------

.. version_tag::
   :label: Since
   :version: 4.0.0


The following section of Studio's configuration overrides allows you to configure settings for the Studio access tokens.  For more information on how access tokens are used, see :ref:`working-with-crafter-studios-api`

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   ##################################################
   ##               Access Tokens                  ##
   ##################################################

   # Issuer for the generated access tokens
   studio.security.token.issuer: ${env:STUDIO_TOKEN_ISSUER}
   # List of accepted issuers for validation of access tokens (separated by commas)
   studio.security.token.validIssuers: ${env:STUDIO_TOKEN_VALID_ISSUERS}
   # The audience for generation and validation of access tokens (if empty the instance id will be used)
   studio.security.token.audience: ${env:STUDIO_TOKEN_AUDIENCE}
   # Time in minutes for the expiration of the access tokens
   studio.security.token.timeout: ${env:STUDIO_TOKEN_TIMEOUT}
   # Password for signing the access tokens (needs to be equal or greater than 512 bits in length)
   studio.security.token.password.sign: ${env:STUDIO_TOKEN_SIGN_PASSWORD}
   # Password for encrypting the access tokens
   studio.security.token.password.encrypt: ${env:STUDIO_TOKEN_ENCRYPT_PASSWORD}
   # Name of the cookie to store the refresh token
   studio.security.token.cookie.name: ${env:STUDIO_REFRESH_TOKEN_NAME}
   # Time in seconds for the expiration of the refresh token cookie
   studio.security.token.cookie.maxAge: ${env:STUDIO_REFRESH_TOKEN_MAX_AGE}
   # Indicates if the refresh token cookie should be secure (should be true for production environments behind HTTPS)
   studio.security.token.cookie.secure: ${env:STUDIO_REFRESH_TOKEN_SECURE}

.. _crafterSite-cookie-domain:

-------------------------
crafterSite Cookie Domain
-------------------------
.. version_tag::
   :label: Since
   :version: 4.0.1

The following section of Studio's configuration overrides allows you to set the ``crafterSite`` cookie at the base domain instead of a subdomain, to allow visibility of the ``crafterSite`` cookie across subdomains.

.. code-block:: yaml
   :caption: *CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/studio/extension/studio-config-override.yaml*
   :linenos:

   # Use base domain instead of subdomain for the crafterSite cookie
   studio.cookie.useBaseDomain: false