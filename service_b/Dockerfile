FROM python:3.11-slim

ENV SERVICE_A_URL=http://service-a

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN useradd -m webapp
USER webapp

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
