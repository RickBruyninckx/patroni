.. _environment:

Environment Configuration Settings
==================================

It is possible to override some of the configuration parameters defined in the Patroni configuration file using the system environment variables. This document lists all environment variables handled by Patroni. The values set via those variables always take precedence over the ones set in the Patroni configuration file.

Global/Universal
----------------
-  **PATRONI\_CONFIGURATION**: it is possible to set the entire configuration for the Patroni via ``PATRONI_CONFIGURATION`` environment variable. In this case any other environment variables will not be considered!
-  **PATRONI\_NAME**: name of the node where the current instance of Patroni is running. Must be unique for the cluster.
-  **PATRONI\_NAMESPACE**: path within the configuration store where Patroni will keep information about the cluster. Default value: "/service"
-  **PATRONI\_SCOPE**: cluster name

Log
---
-  **PATRONI\_LOG\_TYPE**: sets the format of logs. Can be either **plain** or **json**. To use **json** format, you must have the :ref:`jsonlogger <extras>` installed. The default value is **plain**.
-  **PATRONI\_LOG\_LEVEL**: sets the general logging level. Default value is **INFO** (see `the docs for Python logging <https://docs.python.org/3.6/library/logging.html#levels>`_)
-  **PATRONI\_LOG\_TRACEBACK\_LEVEL**: sets the level where tracebacks will be visible. Default value is **ERROR**. Set it to **DEBUG** if you want to see tracebacks only if you enable **PATRONI\_LOG\_LEVEL=DEBUG**.
-  **PATRONI\_LOG\_FORMAT**: sets the log formatting string. If the log type is **plain**, the log format should be a string.
   Refer to `the LogRecord attributes <https://docs.python.org/3.6/library/logging.html#logrecord-attributes>`_ for
   available attributes. If the log type is **json**, the log format can be a list in addition to a string. Each list
   item should correspond to LogRecord attributes. Be cautious that only the field name is required, and the **%(**
   and **)** should be omitted. If you wish to print a log field with a different key name, use a dictionary where
   the dictionary key is the log field, and the value is the name of the field you want to be printed in the log.
   Default value is **%(asctime)s %(levelname)s: %(message)s**
-  **PATRONI\_LOG\_DATEFORMAT**: sets the datetime formatting string. (see the `formatTime() documentation <https://docs.python.org/3.6/library/logging.html#logging.Formatter.formatTime>`_)
-  **PATRONI\_LOG\_STATIC\_FIELDS**: add additional fields to the log. This option is only available when the log type is set to **json**. Example ``PATRONI_LOG_STATIC_FIELDS="{app: patroni}"``
-  **PATRONI\_LOG\_MAX\_QUEUE\_SIZE**: Patroni is using two-step logging. Log records are written into the in-memory queue and there is a separate thread which pulls them from the queue and writes to stderr or file. The maximum size of the internal queue is limited by default by **1000** records, which is enough to keep logs for the past 1h20m.
-  **PATRONI\_LOG\_DIR**: Directory to write application logs to. The directory must exist and be writable by the user executing Patroni. If you set this env variable, the application will retain 4 25MB logs by default. You can tune those retention values with `PATRONI_LOG_FILE_NUM` and `PATRONI_LOG_FILE_SIZE` (see below).
-  **PATRONI\_LOG\_MODE**: Permissions for log files (for example, ``0644``). If not specified, permissions will be set based on the current umask value.
-  **PATRONI\_LOG\_FILE\_NUM**: The number of application logs to retain.
-  **PATRONI\_LOG\_FILE\_SIZE**: Size of patroni.log file (in bytes) that triggers a log rolling.
-  **PATRONI\_LOG\_LOGGERS**: Redefine logging level per python module. Example ``PATRONI_LOG_LOGGERS="{patroni.postmaster: WARNING, urllib3: DEBUG}"``
-  **PATRONI\_LOG\_DEDUPLICATE\_HEARTBEAT\_LOGS**: If set to ``true``, successive heartbeat logs that are identical shall not be output. Default value is ``false``.

.. warning::
   The time the HA loop executes at can be very valuable information in diagnosing failovers due to resource exhaustion and similar problems. When ``PATRONI_LOG_DEDUPLICATE_HEARTBEAT_LOGS`` is set to ``true`` there will be no log generated for the HA loop execution (unless the leader changes) and hence this potentially useful information will not be available from the logs.

Citus
-----
Enables integration Patroni with `Citus <https://docs.citusdata.com>`__. If configured, Patroni will take care of registering Citus worker nodes on the coordinator. You can find more information about Citus support :ref:`here <citus>`.

-  **PATRONI\_CITUS\_GROUP**: the Citus group id, integer. Use ``0`` for coordinator and ``1``, ``2``, etc... for workers
-  **PATRONI\_CITUS\_DATABASE**: the database where ``citus`` extension should be created. Must be the same on the coordinator and all workers. Currently only one database is supported.

Consul
------
-  **PATRONI\_CONSUL\_HOST**: the host:port for the Consul local agent.
-  **PATRONI\_CONSUL\_URL**: url for the Consul local agent, in format: http(s)://host:port
-  **PATRONI\_CONSUL\_PORT**: (optional) Consul port
-  **PATRONI\_CONSUL\_SCHEME**: (optional) **http** or **https**, defaults to **http**
-  **PATRONI\_CONSUL\_TOKEN**: (optional) ACL token
-  **PATRONI\_CONSUL\_VERIFY**: (optional) whether to verify the SSL certificate for HTTPS requests
-  **PATRONI\_CONSUL\_CACERT**: (optional) The ca certificate. If present it will enable validation.
-  **PATRONI\_CONSUL\_CERT**: (optional) File with the client certificate
-  **PATRONI\_CONSUL\_KEY**: (optional) File with the client key. Can be empty if the key is part of certificate.
-  **PATRONI\_CONSUL\_DC**: (optional) Datacenter to communicate with. By default the datacenter of the host is used.
-  **PATRONI\_CONSUL\_CONSISTENCY**: (optional) Select consul consistency mode. Possible values are ``default``, ``consistent``, or ``stale`` (more details in `consul API reference <https://www.consul.io/api/features/consistency.html/>`__)
-  **PATRONI\_CONSUL\_CHECKS**: (optional) list of Consul health checks used for the session. By default an empty list is used.
-  **PATRONI\_CONSUL\_REGISTER\_SERVICE**: (optional) whether or not to register a service with the name defined by the scope parameter and the tag master, primary, replica, or standby-leader depending on the node's role. Defaults to **false**
-  **PATRONI\_CONSUL\_SERVICE\_TAGS**: (optional) additional static tags to add to the Consul service apart from the role (``primary``/``replica``/``standby-leader``). By default an empty list is used.
-  **PATRONI\_CONSUL\_SERVICE\_CHECK\_INTERVAL**: (optional) how often to perform health check against registered url
-  **PATRONI\_CONSUL\_SERVICE\_CHECK\_TLS\_SERVER\_NAME**: (optional) override SNI host when connecting via TLS, see also `consul agent check API reference <https://www.consul.io/api-docs/agent/check#tlsservername>`__.

Etcd
----

-  **PATRONI\_ETCD\_PROXY**: proxy url for the etcd. If you are connecting to the etcd using proxy, use this parameter instead of **PATRONI\_ETCD\_URL**
-  **PATRONI\_ETCD\_URL**: url for the etcd, in format: http(s)://(username:password@)host:port
-  **PATRONI\_ETCD\_HOSTS**: list of etcd endpoints in format 'host1:port1','host2:port2',etc...
-  **PATRONI\_ETCD\_USE\_PROXIES**: If this parameter is set to true, Patroni will consider **hosts** as a list of proxies and will not perform a topology discovery of etcd cluster but stick to a fixed list of **hosts**.
-  **PATRONI\_ETCD\_PROTOCOL**: http or https, if not specified http is used. If the **url** or **proxy** is specified - will take protocol from them.
-  **PATRONI\_ETCD\_HOST**: the host:port for the etcd endpoint.
-  **PATRONI\_ETCD\_SRV**: Domain to search the SRV record(s) for cluster autodiscovery. Patroni will try to query these SRV service names for specified domain (in that order until first success): ``_etcd-client-ssl``, ``_etcd-client``, ``_etcd-ssl``, ``_etcd``, ``_etcd-server-ssl``, ``_etcd-server``. If SRV records for ``_etcd-server-ssl`` or ``_etcd-server`` are retrieved then ETCD peer protocol is used do query ETCD for available members. Otherwise hosts from SRV records will be used.
-  **PATRONI\_ETCD\_SRV\_SUFFIX**: Configures a suffix to the SRV name that is queried during discovery. Use this flag to differentiate between multiple etcd clusters under the same domain. Works only with conjunction with **PATRONI\_ETCD\_SRV**. For example, if ``PATRONI_ETCD_SRV_SUFFIX=foo`` and ``PATRONI_ETCD_SRV=example.org`` are set, the following DNS SRV query is made:``_etcd-client-ssl-foo._tcp.example.com`` (and so on for every possible ETCD SRV service name).
-  **PATRONI\_ETCD\_USERNAME**: username for etcd authentication.
-  **PATRONI\_ETCD\_PASSWORD**: password for etcd authentication.
-  **PATRONI\_ETCD\_CACERT**: The ca certificate. If present it will enable validation.
-  **PATRONI\_ETCD\_CERT**: File with the client certificate.
-  **PATRONI\_ETCD\_KEY**: File with the client key. Can be empty if the key is part of certificate.

Etcdv3
------
Environment names for Etcdv3 are similar as for Etcd, you just need to use ``ETCD3`` instead of ``ETCD`` in the variable name. Example: ``PATRONI_ETCD3_HOST``, ``PATRONI_ETCD3_CACERT``, and so on.

.. warning::
    Keys created with protocol version 2 are not visible with protocol version 3 and the other way around, therefore it is not possible to switch from Etcd to Etcdv3 just by updating Patroni configuration. In addition, Patroni uses Etcd's gRPC-gateway (proxy) to communicate with the V3 API, which means that TLS common name authentication is not possible.


ZooKeeper
---------
-  **PATRONI\_ZOOKEEPER\_HOSTS**: Comma separated list of ZooKeeper cluster members: "'host1:port1','host2:port2','etc...'". It is important to quote every single entity!
-  **PATRONI\_ZOOKEEPER\_USE\_SSL**: (optional) Whether SSL is used or not. Defaults to ``false``. If set to ``false``, all SSL specific parameters are ignored.
-  **PATRONI\_ZOOKEEPER\_CACERT**: (optional) The CA certificate. If present it will enable validation.
-  **PATRONI\_ZOOKEEPER\_CERT**: (optional) File with the client certificate.
-  **PATRONI\_ZOOKEEPER\_KEY**: (optional) File with the client key.
-  **PATRONI\_ZOOKEEPER\_KEY\_PASSWORD**: (optional) The client key password.
-  **PATRONI\_ZOOKEEPER\_VERIFY**: (optional) Whether to verify certificate or not. Defaults to ``true``.
-  **PATRONI\_ZOOKEEPER\_SET\_ACLS**: (optional) If set, configure Kazoo to apply a default ACL to each ZNode that it creates. ACLs will assume 'x509' schema and should be specified as a dictionary with the principal as the key and one or more permissions as a list in the value.  Permissions may be one of ``CREATE``, ``READ``, ``WRITE``, ``DELETE`` or ``ADMIN``.  For example, ``set_acls: {CN=principal1: [CREATE, READ], CN=principal2: [ALL]}``.
-  **PATRONI\_ZOOKEEPER\_AUTH\_DATA**: (optional) Authentication credentials to use for the connection. Should be a dictionary in the form that `scheme` is the key and `credential` is the value. Defaults to empty dictionary.

.. note::
    It is required to install ``kazoo>=2.6.0`` to support SSL.


Exhibitor
---------
-  **PATRONI\_EXHIBITOR\_HOSTS**: initial list of Exhibitor (ZooKeeper) nodes in format: 'host1,host2,etc...'. This list updates automatically whenever the Exhibitor (ZooKeeper) cluster topology changes.
-  **PATRONI\_EXHIBITOR\_PORT**: Exhibitor port.

.. _kubernetes_environment:

Kubernetes
----------
-  **PATRONI\_KUBERNETES\_BYPASS\_API\_SERVICE**: (optional) When communicating with the Kubernetes API, Patroni is usually relying on the `kubernetes` service, the address of which is exposed in the pods via the `KUBERNETES_SERVICE_HOST` environment variable. If `PATRONI_KUBERNETES_BYPASS_API_SERVICE` is set to ``true``, Patroni will resolve the list of API nodes behind the service and connect directly to them.
-  **PATRONI\_KUBERNETES\_NAMESPACE**: (optional) Kubernetes namespace where the Patroni pod is running. Default value is `default`.
-  **PATRONI\_KUBERNETES\_LABELS**: Labels in format ``{label1: value1, label2: value2}``. These labels will be used to find existing objects (Pods and either Endpoints or ConfigMaps) associated with the current cluster. Also Patroni will set them on every object (Endpoint or ConfigMap) it creates.
-  **PATRONI\_KUBERNETES\_SCOPE\_LABEL**: (optional) name of the label containing cluster name. Default value is `cluster-name`.
-  **PATRONI\_KUBERNETES\_BOOTSTRAP\_LABELS**: (optional) Labels in format ``{label1: value1, label2: value2}``. These labels will be assigned to a Patroni pod when its state is either ``initializing new cluster``, ``running custom bootstrap script``, ``starting after custom bootstrap`` or ``creating replica``.
-  **PATRONI\_KUBERNETES\_ROLE\_LABEL**: (optional) name of the label containing role (`primary`, `replica` or other custom value). Patroni will set this label on the pod it runs in. Default value is ``role``.
-  **PATRONI\_KUBERNETES\_LEADER\_LABEL\_VALUE**: (optional) value of the pod label when Postgres role is `primary`. Default value is `primary`.
-  **PATRONI\_KUBERNETES\_FOLLOWER\_LABEL\_VALUE**: (optional) value of the pod label when Postgres role is `replica`. Default value is `replica`.
-  **PATRONI\_KUBERNETES\_STANDBY\_LEADER\_LABEL\_VALUE**: (optional) value of the pod label when Postgres role is ``standby_leader``. Default value is ``primary``.
-  **PATRONI\_KUBERNETES\_TMP\_ROLE\_LABEL**: (optional) name of the temporary label containing role (`primary` or `replica`). Value of this label will always use the default of corresponding role. Set only when necessary.
-  **PATRONI\_KUBERNETES\_USE\_ENDPOINTS**: (optional) if set to true, Patroni will use Endpoints instead of ConfigMaps to run leader elections and keep cluster state.
-  **PATRONI\_KUBERNETES\_POD\_IP**: (optional) IP address of the pod Patroni is running in. This value is required when `PATRONI_KUBERNETES_USE_ENDPOINTS` is enabled and is used to populate the leader endpoint subsets when the pod's PostgreSQL is promoted.
-  **PATRONI\_KUBERNETES\_PORTS**: (optional) if the Service object has the name for the port, the same name must appear in the Endpoint object, otherwise service won't work. For example, if your service is defined as ``{Kind: Service, spec: {ports: [{name: postgresql, port: 5432, targetPort: 5432}]}}``, then you have to set ``PATRONI_KUBERNETES_PORTS='[{"name": "postgresql", "port": 5432}]'`` and Patroni will use it for updating subsets of the leader Endpoint. This parameter is used only if `PATRONI_KUBERNETES_USE_ENDPOINTS` is set.
-  **PATRONI\_KUBERNETES\_CACERT**: (optional) Specifies the file with the CA_BUNDLE file with certificates of trusted CAs to use while verifying Kubernetes API SSL certs. If not provided, patroni will use the value provided by the ServiceAccount secret.
-  **PATRONI\_RETRIABLE\_HTTP\_CODES**: (optional) list of HTTP status codes from K8s API to retry on. By default Patroni is retrying on ``500``, ``503``, and ``504``, or if K8s API response has ``retry-after`` HTTP header.

Raft (deprecated)
-----------------

-  **PATRONI\_RAFT\_SELF\_ADDR**: ``ip:port`` to listen on for Raft connections. The ``self_addr`` must be accessible from other nodes of the cluster. If not set, the node will not participate in consensus.
-  **PATRONI\_RAFT\_BIND\_ADDR**: (optional) ``ip:port`` to listen on for Raft connections. If not specified the ``self_addr`` will be used.
-  **PATRONI\_RAFT\_PARTNER\_ADDRS**: list of other Patroni nodes in the cluster in format ``"'ip1:port1','ip2:port2'"``. It is important to quote every single entity!
-  **PATRONI\_RAFT\_DATA\_DIR**: directory where to store Raft log and snapshot. If not specified the current working directory is used.
-  **PATRONI\_RAFT\_PASSWORD**: (optional) Encrypt Raft traffic with a specified password, requires ``cryptography`` python module.

PostgreSQL
----------
-  **PATRONI\_POSTGRESQL\_LISTEN**: IP address + port that Postgres listens to. Multiple comma-separated addresses are permitted, as long as the port component is appended after to the last one with a colon, i.e. ``listen: 127.0.0.1,127.0.0.2:5432``. Patroni will use the first address from this list to establish local connections to the PostgreSQL node.
-  **PATRONI\_POSTGRESQL\_CONNECT\_ADDRESS**: IP address + port through which Postgres is accessible from other nodes and applications.
-  **PATRONI\_POSTGRESQL\_PROXY\_ADDRESS**: IP address + port through which a connection pool (e.g. pgbouncer) running next to Postgres is accessible. The value is written to the member key in DCS as ``proxy_url`` and could be used/useful for service discovery.
-  **PATRONI\_POSTGRESQL\_DATA\_DIR**: The location of the Postgres data directory, either existing or to be initialized by Patroni.
-  **PATRONI\_POSTGRESQL\_CONFIG\_DIR**: The location of the Postgres configuration directory, defaults to the data directory. Must be writable by Patroni.
-  **PATRONI\_POSTGRESQL\_BIN_DIR**: Path to PostgreSQL binaries. (pg_ctl, initdb, pg_controldata, pg_basebackup, postgres, pg_isready, pg_rewind) The  default value is an empty string meaning that PATH environment variable will be used to find the executables.
-  **PATRONI\_POSTGRESQL\_BIN\_PG\_CTL**: (optional) Custom name for ``pg_ctl`` binary.
-  **PATRONI\_POSTGRESQL\_BIN\_INITDB**: (optional) Custom name for ``initdb`` binary.
-  **PATRONI\_POSTGRESQL\_BIN\_PG\_CONTROLDATA**: (optional) Custom name for ``pg_controldata`` binary.
-  **PATRONI\_POSTGRESQL\_BIN\_PG\_BASEBACKUP**: (optional) Custom name for ``pg_basebackup`` binary.
-  **PATRONI\_POSTGRESQL\_BIN\_POSTGRES**: (optional) Custom name for ``postgres`` binary.
-  **PATRONI\_POSTGRESQL\_BIN\_IS\_READY**: (optional) Custom name for ``pg_isready`` binary.
-  **PATRONI\_POSTGRESQL\_BIN\_PG\_REWIND**: (optional) Custom name for ``pg_rewind`` binary.
-  **PATRONI\_POSTGRESQL\_PGPASS**: path to the `.pgpass <https://www.postgresql.org/docs/current/static/libpq-pgpass.html>`__ password file. Patroni creates this file before executing pg\_basebackup and under some other circumstances. The location must be writable by Patroni.
-  **PATRONI\_REPLICATION\_USERNAME**: replication username; the user will be created during initialization. Replicas will use this user to access the replication source via streaming replication
-  **PATRONI\_REPLICATION\_PASSWORD**: replication password; the user will be created during initialization.
-  **PATRONI\_REPLICATION\_SSLMODE**: (optional) maps to the `sslmode <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLMODE>`__ connection parameter, which allows a client to specify the type of TLS negotiation mode with the server. For more information on how each mode works, please visit the `PostgreSQL documentation <https://www.postgresql.org/docs/current/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS>`__. The default mode is ``prefer``.
-  **PATRONI\_REPLICATION\_SSLKEY**: (optional) maps to the `sslkey <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLKEY>`__ connection parameter, which specifies the location of the secret key used with the client's certificate.
-  **PATRONI\_REPLICATION\_SSLPASSWORD**: (optional) maps to the `sslpassword <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLPASSWORD>`__ connection parameter, which specifies the password for the secret key specified in ``PATRONI_REPLICATION_SSLKEY``.
-  **PATRONI\_REPLICATION\_SSLCERT**: (optional) maps to the `sslcert <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCERT>`__ connection parameter, which specifies the location of the client certificate.
-  **PATRONI\_REPLICATION\_SSLROOTCERT**: (optional) maps to the `sslrootcert <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLROOTCERT>`__ connection parameter, which specifies the location of a file containing one or more certificate authorities (CA) certificates that the client will use to verify a server's certificate.
-  **PATRONI\_REPLICATION\_SSLCRL**: (optional) maps to the `sslcrl <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCRL>`__ connection parameter, which specifies the location of a file containing a certificate revocation list. A client will reject connecting to any server that has a certificate present in this list.
-  **PATRONI\_REPLICATION\_SSLCRLDIR**: (optional) maps to the `sslcrldir <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCRLDIR>`__ connection parameter, which specifies the location of a directory with files containing a certificate revocation list. A client will reject connecting to any server that has a certificate present in this list.
-  **PATRONI\_REPLICATION\_SSLNEGOTIATION**: (optional) maps to the `sslnegotiation <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLNEGOTIATION>`__ connection parameter, which controls how SSL encryption is negotiated with the server, if SSL is used.
-  **PATRONI\_REPLICATION\_GSSENCMODE**: (optional) maps to the `gssencmode <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-GSSENCMODE>`__ connection parameter, which determines whether or with what priority a secure GSS TCP/IP connection will be negotiated with the server
-  **PATRONI\_REPLICATION\_CHANNEL\_BINDING**: (optional) maps to the `channel_binding <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-CHANNEL-BINDING>`__ connection parameter, which controls the client's use of channel binding.
-  **PATRONI\_SUPERUSER\_USERNAME**: name for the superuser, set during initialization (initdb) and later used by Patroni to connect to the postgres. Also this user is used by pg_rewind.
-  **PATRONI\_SUPERUSER\_PASSWORD**: password for the superuser, set during initialization (initdb).
-  **PATRONI\_SUPERUSER\_SSLMODE**: (optional) maps to the `sslmode <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLMODE>`__ connection parameter, which allows a client to specify the type of TLS negotiation mode with the server. For more information on how each mode works, please visit the `PostgreSQL documentation <https://www.postgresql.org/docs/current/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS>`__. The default mode is ``prefer``.
-  **PATRONI\_SUPERUSER\_SSLKEY**: (optional) maps to the `sslkey <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLKEY>`__ connection parameter, which specifies the location of the secret key used with the client's certificate.
-  **PATRONI\_SUPERUSER\_SSLPASSWORD**: (optional) maps to the `sslpassword <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLPASSWORD>`__ connection parameter, which specifies the password for the secret key specified in ``PATRONI_SUPERUSER_SSLKEY``.
-  **PATRONI\_SUPERUSER\_SSLCERT**: (optional) maps to the `sslcert <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCERT>`__ connection parameter, which specifies the location of the client certificate.
-  **PATRONI\_SUPERUSER\_SSLROOTCERT**: (optional) maps to the `sslrootcert <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLROOTCERT>`__ connection parameter, which specifies the location of a file containing one or more certificate authorities (CA) certificates that the client will use to verify a server's certificate.
-  **PATRONI\_SUPERUSER\_SSLCRL**: (optional) maps to the `sslcrl <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCRL>`__ connection parameter, which specifies the location of a file containing a certificate revocation list. A client will reject connecting to any server that has a certificate present in this list.
-  **PATRONI\_SUPERUSER\_SSLCRLDIR**: (optional) maps to the `sslcrldir <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCRLDIR>`__ connection parameter, which specifies the location of a directory with files containing a certificate revocation list. A client will reject connecting to any server that has a certificate present in this list.
-  **PATRONI\_SUPERUSER\_SSLNEGOTIATION**: (optional) maps to the `sslnegotiation <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLNEGOTIATION>`__ connection parameter, which controls how SSL encryption is negotiated with the server, if SSL is used.
-  **PATRONI\_SUPERUSER\_GSSENCMODE**: (optional) maps to the `gssencmode <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-GSSENCMODE>`__ connection parameter, which determines whether or with what priority a secure GSS TCP/IP connection will be negotiated with the server
-  **PATRONI\_SUPERUSER\_CHANNEL\_BINDING**: (optional) maps to the `channel_binding <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-CHANNEL-BINDING>`__ connection parameter, which controls the client's use of channel binding.
-  **PATRONI\_REWIND\_USERNAME**: (optional) name for the user for ``pg_rewind``; the user will be created during initialization of postgres 11+ and all necessary `permissions <https://www.postgresql.org/docs/11/app-pgrewind.html#id-1.9.5.8.8>`__ will be granted.
-  **PATRONI\_REWIND\_PASSWORD**: (optional) password for the user for ``pg_rewind``; the user will be created during initialization.
-  **PATRONI\_REWIND\_SSLMODE**: (optional) maps to the `sslmode <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLMODE>`__ connection parameter, which allows a client to specify the type of TLS negotiation mode with the server. For more information on how each mode works, please visit the `PostgreSQL documentation <https://www.postgresql.org/docs/current/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS>`__. The default mode is ``prefer``.
-  **PATRONI\_REWIND\_SSLKEY**: (optional) maps to the `sslkey <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLKEY>`__ connection parameter, which specifies the location of the secret key used with the client's certificate.
-  **PATRONI\_REWIND\_SSLPASSWORD**: (optional) maps to the `sslpassword <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLPASSWORD>`__ connection parameter, which specifies the password for the secret key specified in ``PATRONI_REWIND_SSLKEY``.
-  **PATRONI\_REWIND\_SSLCERT**: (optional) maps to the `sslcert <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCERT>`__ connection parameter, which specifies the location of the client certificate.
-  **PATRONI\_REWIND\_SSLROOTCERT**: (optional) maps to the `sslrootcert <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLROOTCERT>`__ connection parameter, which specifies the location of a file containing one or more certificate authorities (CA) certificates that the client will use to verify a server's certificate.
-  **PATRONI\_REWIND\_SSLCRL**: (optional) maps to the `sslcrl <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCRL>`__ connection parameter, which specifies the location of a file containing a certificate revocation list. A client will reject connecting to any server that has a certificate present in this list.
-  **PATRONI\_REWIND\_SSLCRLDIR**: (optional) maps to the `sslcrldir <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLCRLDIR>`__ connection parameter, which specifies the location of a directory with files containing a certificate revocation list. A client will reject connecting to any server that has a certificate present in this list.
-  **PATRONI\_REWIND\_SSLNEGOTIATION**: (optional) maps to the `sslnegotiation <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLNEGOTIATION>`__ connection parameter, which controls how SSL encryption is negotiated with the server, if SSL is used.
-  **PATRONI\_REWIND\_GSSENCMODE**: (optional) maps to the `gssencmode <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-GSSENCMODE>`__ connection parameter, which determines whether or with what priority a secure GSS TCP/IP connection will be negotiated with the server
-  **PATRONI\_REWIND\_CHANNEL\_BINDING**: (optional) maps to the `channel_binding <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-CHANNEL-BINDING>`__ connection parameter, which controls the client's use of channel binding.

REST API
--------
-  **PATRONI\_RESTAPI\_CONNECT\_ADDRESS**: IP address and port to access the REST API.
-  **PATRONI\_RESTAPI\_LISTEN**: IP address and port that Patroni will listen to, to provide health-check information for HAProxy.
-  **PATRONI\_RESTAPI\_USERNAME**: Basic-auth username to protect unsafe REST API endpoints.
-  **PATRONI\_RESTAPI\_PASSWORD**: Basic-auth password to protect unsafe REST API endpoints.
-  **PATRONI\_RESTAPI\_CERTFILE**: Specifies the file with the certificate in the PEM format. If the certfile is not specified or is left empty, the API server will work without SSL.
-  **PATRONI\_RESTAPI\_KEYFILE**: Specifies the file with the secret key in the PEM format.
-  **PATRONI\_RESTAPI\_KEYFILE\_PASSWORD**: Specifies a password for decrypting the keyfile.
-  **PATRONI\_RESTAPI\_CAFILE**: Specifies the file with the CA_BUNDLE with certificates of trusted CAs to use while verifying client certs.
-  **PATRONI\_RESTAPI\_CIPHERS**: (optional) Specifies the permitted cipher suites (e.g. "ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:!SSLv1:!SSLv2:!SSLv3:!TLSv1:!TLSv1.1")
-  **PATRONI\_RESTAPI\_VERIFY\_CLIENT**: ``none`` (default), ``optional`` or ``required``. When ``none`` REST API will not check client certificates. When ``required`` client certificates are required for all REST API calls. When ``optional`` client certificates are required for all unsafe REST API endpoints. When ``required`` is used, then client authentication succeeds, if the certificate signature verification succeeds. For ``optional`` the client cert will only be checked for ``PUT``, ``POST``, ``PATCH``, and ``DELETE`` requests.
-  **PATRONI\_RESTAPI\_ALLOWLIST**: (optional): Specifies the set of hosts that are allowed to call unsafe REST API endpoints. The single element could be a host name, an IP address or a network address using CIDR notation. By default ``allow all`` is used. In case if ``allowlist`` or ``allowlist_include_members`` are set, anything that is not included is rejected.
-  **PATRONI\_RESTAPI\_ALLOWLIST\_INCLUDE\_MEMBERS**: (optional): If set to ``true`` it allows accessing unsafe REST API endpoints from other cluster members registered in DCS (IP address or hostname is taken from the members ``api_url``). Be careful, it might happen that OS will use a different IP for outgoing connections.
-  **PATRONI\_RESTAPI\_HTTP\_EXTRA\_HEADERS**: (optional) HTTP headers let the REST API server pass additional information with an HTTP response.
-  **PATRONI\_RESTAPI\_HTTPS\_EXTRA\_HEADERS**: (optional) HTTPS headers let the REST API server pass additional information with an HTTP response when TLS is enabled. This will also pass additional information set in ``http_extra_headers``.
-  **PATRONI\_RESTAPI\_REQUEST\_QUEUE\_SIZE**: (optional): Sets request queue size for TCP socket used by Patroni REST API.  Once the queue is full, further requests get a "Connection denied" error. The default value is 5.
-  **PATRONI\_RESTAPI\_SERVER\_TOKENS**: (optional) Configures the value of the ``Server`` HTTP header.  ``Original`` (default) will expose the original behaviour and display the BaseHTTP and Python versions, e.g. ``BaseHTTP/0.6 Python/3.12.3``. ``Minimal``: The header will contain only the Patroni version, e.g. ``Patroni/4.0.0``. ``ProductOnly``: The header will contain only the product name, e.g. ``Patroni``.

.. warning::

    - The ``PATRONI_RESTAPI_CONNECT_ADDRESS`` must be accessible from all nodes of a given Patroni cluster. Internally Patroni is using it during the leader race to find nodes with minimal replication lag.
    - If you enabled client certificates validation (``PATRONI_RESTAPI_VERIFY_CLIENT`` is set to ``required``), you also **must** provide **valid client certificates** in the ``PATRONI_CTL_CERTFILE``, ``PATRONI_CTL_KEYFILE``, ``PATRONI_CTL_KEYFILE_PASSWORD``. If not provided, Patroni will not work correctly.


CTL
---
-  **PATRONICTL\_CONFIG\_FILE**: (optional) location of the configuration file.
-  **PATRONI\_CTL\_USERNAME**: (optional) Basic-auth username for accessing protected REST API endpoints. If not provided :ref:`patronictl` will use the value provided for REST API "username" parameter.
-  **PATRONI\_CTL\_PASSWORD**: (optional) Basic-auth password for accessing protected REST API endpoints. If not provided :ref:`patronictl` will use the value provided for REST API "password" parameter.
-  **PATRONI\_CTL\_INSECURE**: (optional) Allow connections to REST API without verifying SSL certs.
-  **PATRONI\_CTL\_CACERT**: (optional) Specifies the file with the CA_BUNDLE file or directory with certificates of trusted CAs to use while verifying REST API SSL certs. If not provided :ref:`patronictl` will use the value provided for REST API "cafile" parameter.
-  **PATRONI\_CTL\_CERTFILE**: (optional) Specifies the file with the client certificate in the PEM format.
-  **PATRONI\_CTL\_KEYFILE**: (optional) Specifies the file with the client secret key in the PEM format.
-  **PATRONI\_CTL\_KEYFILE\_PASSWORD**: (optional) Specifies a password for decrypting the client keyfile.
