
notes:
------
friday worked on web hook integration  for existing dev hub project again not able to proceed further because of some wierd issues that not able solve due to lack of overal knowledge. even dev build is pipeline is failing , markus also checked

not able to fully understand all of our tasks steps, pipelines.. 

so i wanted to understand basics before understanding our deployment setup,so decided to learn all steps on my own from scrach. 

	1) since i am comfotable with native kubernetes i have created 2 node kubernetes cluster
	2) installed tekton, triggers
	3) created kaniko task for building images
	4) created container engine secret and assigned to service account
	5) created resources, tasks, pipelines, pipeline runs on my own for a dummy demo project
	6) I am able to create build and push images to conainer registry, and able to deploy application on current namespace,
	7) then created trigger templet, trigger bindings, EventListeners by creating new service account which has access to all trigger resources and API
	8) i have created a route i.e. exposed service and get  URL which can be acceisible from outside world
	9) added that exposed eventlistner URL in git hub repo, triggering happend after the commit process but failed due to "head_commit is not found" issue -- this we can solve.... 

Eventlistener:
-----------------
it creates a pod

sample output.
--------------
{"eventListener":"votecicd-listener","namespace":"default","eventID":"mdqwh"}

Triggerbinding:
---------------
translate incoming request json to params for pipelineruns

Triggertemplate:
------------------
template for pipelineruns to be executed

note:
------
1) it requires single POD to run multiple webhook trigers, 4 pods for tekton (2 for tekton controller, webhook, 2 for triggers (controller, web hook))
2) Unfortunately, EventListeners only support JSON bodies, for other formats we need to write interceptors.. in our case we no need to worry since git hub gives JSOn in case of slack or other tools we need to write.... 
3) even webhook can be created manually also through yaml files...

-----------------------------------------------




install:
--------------------------------------------------------------------------------------------------------------------------------
tekton:

kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml

kaniko:
kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/master/kaniko/kaniko.yaml

triggers:
kubectl apply --filename https://storage.googleapis.com/tekton-releases/triggers/latest/release.yaml

	verify: kubectl get pods --namespace tekton-pipelines

tkn task list | grep kaniko

kubectl get pods -n tekton-pipelines      ( two components webhook and controller)


kubectl api-resources | grep tekton

--------------------------------------------------------------------------------------------------------------------------------


kubectl delete pipelineresources vote-api-image   
kubectl delete pipelineresources vote-api-repo    
kubectl delete pipelineresources vote-ui-image    
kubectl delete pipelineresources vote-ui-repo     



tkn resource list

tkn task list

tkn taskrun list

tkn taskrun logs -f <name>

tkn pipeline list


tkn pipelinerun list


tkn pipelinerun logs -f <name>

tkn eventlistener

tkn triggerbinding

tkn triggertemplate


kubectl apply -f 03-tasks/
kubectl apply -f 04-pipelines/
kubectl apply -f 05-pipelineruns/01-build-deploy-api-pipelinerun.yaml 


tkn task delete oc-apply-deployment  
tkn task delete build-docker-image-from-git-source

tkn pipeline delete build-and-deploy
tkn pipelinerun delete build-deploy-api-pipelinerun




links:
--------------------------------------------------------------------------------------------------------------------------------
https://github.com/arthurk/tekton-example

https://ventuscloud.eu/docs/tutorials/tekton-pipelines

https://github.com/tektoncd/pipeline/blob/master/docs/tutorial.md


https://github.com/tektoncd/pipeline/blob/master/docs/auth.md       === auth with git and docker hub


https://bigkevmcd.github.io/tekton/triggers/slack/commands/2020/03/02/slack-slash-tekton.html --- interceptor


https://github.com/tektoncd/triggers/tree/master/docs/getting-started


https://ruzickap.github.io/k8s-knative-gitlab-harbor/part-08/#create-tekton-triggers-configuration





