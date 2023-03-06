# Week 2 — Distributed Tracing

## HoneyComb   
    
Updated `requirements.txt` with the dependencies required for HoneyComb   
   
```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```   
     
     
We will install these dependencies by using following command:   
   
```sh
pip install -r requirements.txt
```   
   
   
We will add required Imports and Initialize code for application to file app.py    
    
```py
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
```    
    
Initialize tracing and an exporter that can send data to Honeycomb    

```py
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)
```    
   
Initialize automatic instrumentation with Flask    
   
 ```py
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```     
    
And now we will add the required enviroment variables for backend-flask in docker-compose file     
    
```yml
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"
```     
    
Before running the application we will aslo export API key as env variables from our Honeycomb account to our local environment     
    
```sh
export HONEYCOMB_API_KEY=""
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="Cruddur"
```    
     
     
## X-Ray     
     
Updating `requirements.txt` with python dependencies     
    
```py
aws-xray-sdk
```    
    
Installing dependencies with `requirements.txt`         
     
 ```sh
pip install -r requirements.txt
```    
    
Adding required Python Import statements and xray configuredetails to app.py      
      
```py
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.ext.flask.middleware import XRayMiddleware    
          
xray_url = os.getenv("AWS_XRAY_URL")
xray_recorder.configure(service='Cruddur', dynamic_naming=xray_url)
XRayMiddleware(app, xray_recorder)
```     
     
Setting up AWS X-Ray resources we will create a new file xray.json under `aws/json`      
      
```json
{
  "SamplingRule": {
      "RuleName": "Cruddur",
      "ResourceARN": "*",
      "Priority": 9000,
      "FixedRate": 0.1,
      "ReservoirSize": 5,
      "ServiceName": "Cruddur",
      "ServiceType": "*",
      "Host": "*",
      "HTTPMethod": "*",
      "URLPath": "*",
      "Version": 1
  }
}
```     
      
Installing AWS XRay Daemon by following the official AWS documentation       
[Install X-ray Daemon](https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html)    
      
      
Adding Daemon Service to Docker Compose file     
   
```yml
  xray-daemon:
    image: "amazon/aws-xray-daemon"
    environment:
      AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
      AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
      AWS_REGION: "us-east-1"
    command:
      - "xray -o -b xray-daemon:2000"
    ports:
      - 2000:2000/udp
```       
     
     
Adding xray URL and Daemon address as env variables to our backend-flask in docker comppose file     
    
```yml
      AWS_XRAY_URL: "*4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}*"
      AWS_XRAY_DAEMON_ADDRESS: "xray-daemon:2000"
```     
     
Checking Service data for last 10 minutes      
     
```sh
EPOCH=$(date +%s)
aws xray get-service-graph --start-time $(($EPOCH-600)) --end-time $EPOCH
```      
      
      
      
## Rollbar     
     
