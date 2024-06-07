# Use Python 3.7 as the base image
FROM python:3.7-slim

# Set the working directory
WORKDIR /app

# Copy the requirements file into the image
COPY requirements.txt .

# Install the pip dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application code into the image
COPY . .

# Expose the port the app runs on
EXPOSE 5000

# Define the entrypoint for the container
ENTRYPOINT ["python"]
CMD ["app.py"]