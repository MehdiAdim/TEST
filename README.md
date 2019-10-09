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

|  METHODS | REQUEST | DESCRIPTION | BODY formdata | REQUIREMENTS |
| --- | --- | --- | --- | --- | 
| POST | /classify |  | <h5>- Key = *'image'* / Value = *doc.tif*</h5> | | 
|  |  |  |  |  | 
| POST | /fit | | <h5>- Key = 'image' / Value = doc.tif</h5><h5>- Key = *'category'* / Value = *'RIB'* </h5><h5>- Key = *'predicted_as'* / Value = *'RIB'* </h5>||
| GET |  /status | | | | 
| PUT / PATCH | /reset | || | 

- ####    Sending fit request using curl client
```
curl --location --request POST "http://localhost:5000/fit" \
  --form "image=@path_to.tif"
``` 



