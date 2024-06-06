# Use Python 3.6 or later as a base image
FROM python:latest

# Set the working directory
WORKDIR app

# Copy contents into image
COPY .

RUN pwd
RUN ls -la

# Install pip dependencies from requirements
RUN pip install -r requirements.txt

# Set YOUR_NAME environment variable
ENV YOUR_NAME Jonathan

# Expose the correct port
EXPOSE 5500

# Create an entrypoint
ENTRYPOINT ["python", "app.py"]
