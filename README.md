# k8S & helm microservices example and best practices

# livenessProbe
k8S is automatically checking if pod is alive and well; if something is wrong with the pod, it will restart it (autohealing). However, k8S 
does not know on its own if the app that's running inside of a container that's running in that pod is ok. For example, you can have a pod that's not crashing
but the app inside of it is stuck because of some bug. Liveness probe is used to check if the app is healthy. It is configured on container level. This probe
can be a tiny program or a script that's running inside of the container and periodically checks if the app is healthy (by pinging its endpoint). Sometimes
developers will provide this app, and other times it's the devops engineer that should implement it. Another mechanism that's used for this probe is tcpSocket.
In livenessProbe definition, you specify a port on which k8S will try to open a socket. If this socket opens successfully, the app is considered to be alive.
Another mechanism is httpGet. The app itself can expose an endpoint that the liveness probe will hit to check for the health status.
tpSocket example: redis; health-check app example: all the other microservices; httpGet example: not commited, search online

# readinessProbe
Since k8S is aware of the pod state, but not aware of the state of the app that's running inside of it, a mechanism that will allow k8S to 
figure out that the app started successfully is needed. Readiness probe is used for this. It is very similar to liveness probe, but it's used during the app
startup. Some apps take a long time to start (few mins), so if k8S does not know that the app isn't up and running yet, it will start forwarding requests to
it and we will get errors. Readiness probes tells k8S once the startup process is done and the app is up.

# resources
We should tell k8S what cpu and memory resources our apps need; we get this info from developers; 
resources:requests: this defines the cpu and memory that our app needs to run
resources:limits: this defines the limits for our app; if the app uses more than what's specified here, that is not ok and it's probably a bug, so k8s should
kill its pod; 
cpu is expressed in millicores - millicores indicate the relative share of the collective CPU pool available on a node; this abstracts away the actual
underlying hardware - basically whatever number of physical CPUs/cores you have on the node, it is treated as a collective pool of CPU, and millicores are relative to this collective pool.
Memory is expressed in Mi (mebibytes): 1Ki(kibibyte) = 2^10B; 1Mi(mebybyte) = 2^10 * 2^10 B = 2^20 B

# Some security best practices
1.scan images for vulnerabilities
2.don't run containers with root access
3.update k8S to latest version

# helm
helm create microservice - to create helmchart named microservice (helmchart)
microservices/Chart.yaml        - contains chart metadata
microservices/charts/           - contains dependencies (other charts, which we don't have in this example)
microservices/templates/        - core directory, this is where most helm work is done
microservices/values/           - this is where we write the values that are substituted in templates

You should use camel case for your helm template variables.

helm template -f helm-values/emailservice-values.yaml microservice/ - this only check if you are going to generate a valid k8S manifest, but does not generate it
helm template -f /your/values/file                    /chart/directory

helm lint -f helm-values/emailservice-values.yaml microservice/     - to lint the file

helm install -f  helm-values/emailservice-values.yaml   emailservice  microservice      - to deploy emailservice to k8S cluster
                 values-file                            release-name  chart-directory   - release-name is a term from helm world


You can overwrite defined values from command line with helm commands.


