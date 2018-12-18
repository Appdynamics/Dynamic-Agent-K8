
Configuration variables can be included in the machine-agent.yaml file, or obviously in a configMap + secret.
However you do it, please  include the following variables in your configuration:

All parameters:

IS_K8_DEPLOYMENT
CONTROLLER_HOST
CONTROLLER_PORT
CONTROLLER_SSL_ENABLED
ACCOUNT_NAME
ACCOUNT_ACCESS_KEY
APPLICATION_NAME
APPLICATION_NAME_FROM
APPLICATION_NAME_FROM_VALUE
EVENT_SERVICE_URL
GLOBAL_ACCOUNT_NAME
TIER_NAME_FROM
TIER_NAME_FROM_VALUE
INCLUDE_FILTER
EXCLUDE_FILTER


Required Parameters

IS_K8_DEPLOYMENT
Must be "true", otherwise the Dynamic Agent doesn't know it's in a K8 environment.

CONTROLLER_HOST
Notes: Should not include http/https or port number
Example: CONTROLLER_HOST=appd.mydomain.com
 
CONTROLLER_PORT
Notes: Port number of controller
Example: CONTROLLER_PORT=8090
 
CONTROLLER_SSL_ENABLED=false
Possible values: Either true or false
Example: CONTROLLER_SSL_ENABLED=false
 
ACCOUNT_NAME
Notes: Name value found on License page of controller
Example: ACCOUNT_NAME=customer1
 
ACCOUNT_ACCESS_KEY
Notes: Access Key value found on License page of controller
Example: ACCOUNT_ACCESS_KEY=a5f426acdff0

APPLICATION_NAME or APPLICATION_NAME_FROM + APPLICATION_NAME_FROM_VALUE
If all are populated, APPLICATION_NAME will be used as the default if the application name label cannot be found.

APPLICATION_NAME
Notes: Do not use spaces in the Application Name
Example: APPLICATION_NAME=MyApp
 
APPLICATION_NAME_FROM
Possible values: CONTAINER_LABEL
Example: APPLICATION_NAME_FROM=CONTAINER_LABEL

APPLICATION_NAME_FROM_VALUE
Example: APPLICATION_NAME_FROM_VALUE=application-name


Optional Parameters
 
TIER_NAME_FROM
Notes: See section below - What is a tier?
Possible values: HOSTNAME, CONTAINER_NAME, CONTAINER_LABEL, or JVM_PARAM
Example: TIER_NAME_FROM=CONTAINER_NAME
 
TIER_NAME_FROM_VALUE
Notes: See section below - What is a tier?
Example: TIER_NAME_FROM_VALUE=DAPPD_TIER_NAME

EVENT_SERVICE_URL and GLOBAL_ACCOUNT_NAME are both necessary to enable analytics reporting from the instrumented containers
 
EVENT_SERVICE_URL
Notes: The host and port of of the Events Service this application should report to.
       Should not include http/https, but it should include the port
Example: EVENT_SERVICE_URL=appd.mydomain.com:9080
 
GLOBAL_ACCOUNT_NAME
Notes: Global Account Name value found on License page of controller
Example: GLOBAL_ACCOUNT_NAME=customer1_32458762934
 
What is a tier?

By default, the io.kubernetes.container.name label of the container will be used as the tier name in AppDynamics. If you want to change this, there are some optional parameters you can set in machine-agent.yaml.

TIER_NAME_FROM

TIER_NAME_FROM=HOSTNAME or if left blank or not included in controller.env, it will use the hostname for the tier 
TIER_NAME_FROM=CONTAINER_NAME will cause the process to use the name of the container for the tier name
TIER_NAME_FROM=CONTAINER_LABEL will cause the process to use the value of the container label specified in TIER_NAME_FROM_VALUE
TIER_NAME_FROM=JVM_PARAM will cause the process to look for the JVM param specified in TIER_NAME_FROM_VALUE 
For example, the following controller.env file will result in the process looking for a JVM parameter -Dservice-name, and using that value for the tier name in AppDynamics.

CONTROLLER_HOST=my.controller.com
CONTROLLER_PORT=8090
CONTROLLER_SSL_ENABLED=false
APPLICATION_NAME=Jetty
ACCOUNT_NAME=customer1
ACCOUNT_ACCESS_KEY=4e76-a7f8-a5f426acdff0
TIER_NAME_FROM=JVM_PARAM
TIER_NAME_FROM_VALUE=Dservice-name


Filtering Processes
There are two more optional parameters for the controller.env file that you can use to customize which processes are included or excluded. They both use regular expressions that will be applied to process command strings.

INCLUDE_FILTER

This will be used to identify which processes to attach to. If no value is provided, the process will only look for "java" in the process command string.

EXCLUDE_FILTER

If you know of certain processes that you definitely don't want to instrument, you can provide a regular expression here to filter them out. For example, if no EXCLUDE_FILTER is provided, the process will exclude any Java processes that contain "Dappdynamics.controller.hostName" in the command string. This will prevent any already-instrumented processes, such as the machine agent, from getting processed.

