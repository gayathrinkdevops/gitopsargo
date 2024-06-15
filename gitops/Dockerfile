FROM python:3.9-slim-buster
RUN pip install flask
WORKDIR /mnt/c/Users/Lenovo/Documents/gitopsargo/app.py
COPY app.py .
EXPOSE 5000
ENTRYPOINT ["python", "app.py"]