#Binary Builds deployments on OpenShift

Just a simple spring boot application, used for testing on OpenShift. To demonstrate how to perform binary builds in OpenShift, let’s walk through steps:

1. Create project on OCP using CLI:

oc new-project spring-binary

2. Now, navigate to the templates folder in the Git repository and instantiate the template which will create the necessary objects in OpenShift:

oc process -f spring-binary-build.json | oc create -f -

After the template has been instantiated, a build is automatically triggered.

oc get builds
NAME                TYPE      FROM      STATUS    STARTED          DURATION
spring-binary-app-1   Source    Binary    Running   34 seconds ago   32s

Recall that binary builds does not retrieve source code, but expects it to be provided. We can confirm this by viewing the logs of the pods:

oc logs spring-binary-app-1-build

3. The builder is expecting some form of input at standard in for it to begin the build process. Since none was provided, it will sit and wait forever. Let’s go ahead and cancel this build, and trigger a new build while also providing the source content

oc cancel-build cakephp-binary-app-1

4. To upload artifcat S2i builds, any artifacts found in a folder called deployments will be automatically deployed. In the sample repository, a folder called artifcat  contains a folder called deployments with the artifact that should be deployed.
The artifact is called test4-0.0.1-SNAPSHOT.jar so that it is automatically deployed to the OCP cluster. Navigate to the root of the repository and run the following command to produce the binary content for the build.

oc start-build eap-binary-app --from-dir=artifact

The folder is then uploaded and used as the content for the binary build. Since this is the second build, the logs can be viewed by running oc logs spring-binary-app-2-build -f. 


Run oc get routes to determine the application URL and navigate to this URL in a web browser to confirm the page is accessible.
