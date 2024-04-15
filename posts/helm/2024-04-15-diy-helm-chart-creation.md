<h1>Creating a Helm Chart</h1>
* We now have a basic understanding of what helm is, how it functions, and how its configured. Now we can go ahead and create our own helm chart
* Create a helm chart skeleton
  - `helm create test-helm-chart`: this will create a skeleton helm chart directory that you can modify for your own purposes
* Empty the templates directory and copy and move your own manifest files into that templates folder
  - At this point, the helm chart is basically fully functional, the only problem is that the manifest files are templatized
  - No modifications can be made and only the static values specified in those manifest files can ever be created by helm

