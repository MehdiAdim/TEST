# MLBOOTSRAP API

### 1. Installation guide on unix based OS

```bash
# cloning repo
git clone https://tessilab.ddns.net/python/bootstrapping/auto-classification.git
cd auto-classification/

pip install --upgrade pip
# should use a virtualenv or another virtual envirement
pip install virtualenv

# Not use inside an older version of python (python 3.6 is required)
# create the virtual env with python3.6 or newer
virtualenv -p python3.6 venv  # replace python3.6 with path_to_python 3.6
source venv/bin/activate

# install app's requirements
pip install -r requirements.txt
python api.py

# chack if the server and app are running
curl --location --request GET "http://localhost:5000/"
```



### 2. Endpoints  
- ####    Postman published documentation
https://documenter.getpostman.com/view/8206298/SVtPYWoX?version=latest

|  METHODS | REQUEST | BODY formdata | RESPONSES |
| --- | --- | --- | --- | 
| POST | /classify | <h5> Key = *'image'* / Value = *doc.tif*</h5> | <h5>{</h5><h5>"message": "",</h5><h5>"predictedClass": null,</h5><h5>"probability": -1.0,</h5><h5>"success": false,</h5><h5> "usable": false</h5><h5>}</h5>| 
|  |  |  |  |  | 
| POST | /fit |<h5> Key = 'image' / Value = doc.tif</h5><h5> Key = *'category'* / Value = *'RIB'* </h5><h5> Key = *'predicted_as'* / Value = *'ADH'* </h5>|<h5><h5>{</h5><h5>"message": "Process of rib.tif",</h5><h5>"success": true}</h5>|
|  |  |  |  |  | 
| GET |  /status | | <h5>{</h5><h5>"message": "",</h5><h5>"status": "KO",</h5><h5>"success": true,</h5<h5>"usable_categories": []</h5><h5></h5><h5>}</h5>| 
| PUT / PATCH | /reset ||<h5>{</h5><h5>"message": "Reset Done",</h5><h5>"success": true</h5><h5>}</h5>| 

- ####    Sending fit request using curl client
```
curl --location --request POST "http://localhost:5000/fit" \
  --form "image=@path_to.tif"
``` 



