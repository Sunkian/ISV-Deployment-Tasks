# <p align="center"> ⚜️ Alcor ISV Application Deployment ⚜️ </p>

### <p align="center"> Facemask inference AI-engine on Google Coral Dev Board (tflite_coral_tpu)

</br>
</p>

## 1. Simple app - Facemask detection on remote server + Docker (Lite version)✅

_--completed on Sprint 7-- (facemask-detection-tflite)_ </br>
_Note --The original version (non lite) was completed on Sprint 5-- (FaceMask_DetectionV2)_

### **Task:** Run the original facemask detection on André's server

### **Description:** Tflite version of the facemask AI model

</br>
<details><summary><b>Show instructions</b></summary>
1. On a terminal, start the input rstp stream towards the coral's ip address:

    fmpeg -f avfoundation -framerate 30 -video_size 1280x720 -i "0:none" -f rtsp -rtsp_transport tcp rtsp://10.60.17.217:8554/alicewebcam
    OR
    ffmpeg -re -stream_loop -1 -i FaceMaskedTeam.mp4 -f rtsp -rtsp_transport tcp rtsp://10.60.17.217:8554/teamvideo

</br>
2. Then connect via ssh to André's server, go inside the appropriate folder and export the environment variables

    ssh andre@10.60.16.156
    cd apagnoux/facemask-detection-tflite
    export INPUT_RTSP_URL=rtsp://10.60.16.156:8554/alicewebcam
    export OUTPUT_RTSP_URL=rtsp://10.60.16.156:8554/facemaskalice
    export MQTT_URL=10.60.16.156
    export MQTT_TOPIC=topic/facemask_test

</br>
3. Select and activate a virtual environment

    source ../env/bin/activate

</br>
4. Finally, start the python script:

    python main.py

</br>
5. To run the same app with Docker:

    docker run -e INPUT_RTSP_URL=rtsp://10.60.16.156:8554/alicewebcam -e OUTPUT_RTSP_URL=rtsp://10.60.16.156:8554/facemaskalice -e MQTT_URL=10.60.16.156 -e MQTT_TOPIC=topic/facemask_test alcor-docker.cisco.com/facemake-tflite:0.0.3

</details>

</br> </br>

## 1 bis. Simple app - Emotion detection on remote server + Docker ✅

_--completed on Sprint 5--_

### **Task:** Run the original emotion detection on André's server (Rainbow project)

</br>
<details><summary><b>Show instructions</b></summary>
5. To run the same app with Docker:

    docker run -e INPUT_RTSP_URL=rtsp://10.60.16.156:8554/alicewebcam -e OUTPUT_RTSP_URL=rtsp://10.60.16.156:8554/facemaskalice -e MQTT_URL=10.60.16.156 -e MQTT_TOPIC=topic/facemask_test appedge-docker.cisco.com/emotion_detection

</details>

</br> </br>

## 2. Dockerization for the facemask detection tflite version ✅

_--completed on Sprint 9--_

### **Task:** Containerize the app in a Docker container and make the inference run on the Coral

</br>
<details><summary><b>Show instructions</b></summary>
1. On a terminal, start the input rstp stream towards the coral's ip address:

    ffmpeg -f avfoundation -framerate 30 -video_size 1280x720 -i "0:none" -f rtsp -rtsp_transport tcp rtsp://10.60.17.217:8554/alicewebcam

</br>
2. Directly on the Coral, start the Docker images to enable RTSP streams and MQTT Broker:

    ssh mendel@10.60.17.217

    docker run -d -it -p 1883:1883 -p 9001:9001 --name mosquitto -v /home/mendel/mosquitto.conf:/mosquitto/config/mosquitto.conf eclipse-mosquitto

    docker run -d --rm -it --network=host aler9/rtsp-simple-server

</br>
3. Finally, start the facemask detection AI app, which docker image is tflite:0.0.2:

    docker run -it --privileged -e MODEL=mobilenet_v1_face_mask_quantized_edgetpu.tflite -e LABELS=facemask_labels.txt -e INPUT_RTSP_URL=rtsp://10.60.17.217:8554/alicewebcam -e OUTPUT_RTSP_URL=rtsp://10.60.17.217:8554/facemaskalice -e MQTT_URL=10.60.17.217 -e MQTT_TOPIC=topic/facemask_test tflite:0.0.2

</details>
</br>
Parameters that can be changed:

`--MODEL`: Path of the `.tflite` model. The model needs to be a fully quantized model in order to be compatible for the edge TPUs. </br>
`--LABELS`: Path of the labels associated to the `.tflite` model. </br>
`--INPUT_RTSP_URL`: </br>
`--OUTPUT_RTSP_URL`: </br>
`--MQTT_URL`: </br>
`--MQTT_TOPIC`:

</br> </br>

## 3. Docker compose ✅

_--completed on Sprint 9--_

<details><summary><b>Show instructions</b></summary>
1. On a terminal, start the input rstp stream towards the coral's ip address:

    ssh mendel@10.60.17.217

    ffmpeg -f avfoundation -framerate 30 -video_size 1280x720 -i "0:none" -f rtsp -rtsp_transport tcp rtsp://10.60.17.217:8554/alicewebcam

</br>
2. Directly on the Coral, run the docker-compose.yaml file with :

    docker-compose up

</details>

</br> </br>

## 4. Kubernetes ⬜️-> ✅

_--To do on Sprint 10--_

`cd /home/mendel/facemask-detection-tflite-master/kubernetes`

- Create resources from kustomization.yaml file : `kubectl apply -k .`</br>
- Create resources : `kubectl apply -f deployment.yaml` </br>
- Check resources : `kubectl get deployments` / `kubectl get pods` / `kubectl get services`
  </br> </br>

<details><summary><b> 💡 Cheat sheet 💡</b></summary>
1. Delete all the deployments/pods from a specific namespace:

    kubectl delete --all deployments --namespace=tflite-facemask
    kubectl delete --all pods --namespace=tflite-facemask

</br>
2. Delete specific namespace:

    kubectl delete namespace tflite-facemask

</br>
3. Get all the pods from all namespaces:

    kubectl get pods --all-namespaces

</br>
4. Describe all pods from a specific namespace:

    kubectl describe pod --namespace tflite-facemask

</br>
5. Describe a specific pod from a specific namespace:

    kubectl describe pods rtsp-server-d4c858984-p7gjk -n tflite-facemask

</br>
6. Change number of replicas (=stop/start a pod)

    kubectl scale -n tflite-facemask deploy  --replicas=1 tflite-facemask

</br>
7. Check de deployments for a specific namespace

    kubectl -n tflite-facemask get deploy

</br>
8. Methods for restarting Kubernetes Pods:

</details>
</br> </br>

## 5. Nginx ✅

_--Completed Sprint 11-- :_

> APPEDGE 264 -> As an IT Admin I want to able to deploy a simple "Hello World" application which is running within an Nginx container so that I can serve simple web-based applications

_--Completed Sprint 12--:_
> APPEDGE 265 and 266 -> Basic MQTT Web client application which can show it subscribing to messages from the facemask detection AI app in separate container; should able to demonstrate the 3 different containers (1 x demo web UI app, 1 x mqtt broker and 1 x AI app) working together whilst deployed on-prem for now; UI is simple, not doing anything more than just listing messages as they come in, not doing full facemask-type app interface; will be done in an additional story.
