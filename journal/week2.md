# Week 2 — Distributed Tracing

## HONEYCOMB

Create a new environment with the honeycomb APIKEY & using the code in the terminal

```
export HONEYCOMB_API_KEY="[INSERT HONEYCOMB APIKEY HERE]"
gp env HONEYCOMB_API_KEY="[INSERT HONEYCOMB APIKEY HERE]"
```

Hardcoded HONEYCOMB_SERVICE_NAME & configured OTEL to send to Honeycomb by adding the following env vars to ```backend-flask`` in docker compose

```
OTEL_SERVICE_NAME: '[INSERT CUSTOM SERVICE NAME]'
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
```

add to ```backend-flask/requirements.txt```
```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```


Install dependences in ```backend-flask/requirements.txt```
```
pip install -r requirements.txt
```

add to ```backend-flask/app.py```
```
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.sdk.trace.export import ConsoleSpanExporter, SimpleSpanProcessor


# for honeycomb
# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)

# Show this in the logs within the backend-flask app (STDOUT)
simple_processor = SimpleSpanProcessor(ConsoleSpanExporter())
provider.add_span_processor(simple_processor)

trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)
```

add this below the line with the code ```app = Flask(__name__)``` in backend-flask/app.py file

```
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```
