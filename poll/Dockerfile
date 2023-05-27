FROM python:3.11.2-alpine3.17
WORKDIR /poll
COPY . /poll
EXPOSE 80
RUN pip3 install -r requirements.txt
CMD ["flask", "run", "--host=0.0.0.0", "--port=80"]