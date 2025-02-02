# World Time App

## About the Project
The **World Time App** is a lightweight, user-friendly application that displays the current time for various countries and their capitals. It allows users to view both **digital** and **analog clock formats** for over 190 countries around the globe. Users can select a country or search by country or capital name, making the app intuitive and easy to navigate.

The project is designed with **modern web technologies** and utilizes a microservices architecture with Docker containers for the frontend and backend. This ensures flexibility, scalability, and portability.

---

## Features
1. **Search Functionality**: Users can search for a country or capital and view the current time in that region.
2. **Interactive Clock**: Displays both digital and analog clocks for the selected location.
3. **RESTful API**: The backend exposes a `/time` endpoint for querying the current time based on the selected country.
4. **CORS Enabled**: The backend allows requests from the frontend by enabling CORS.
5. **Dockerized Deployment**: The application is containerized for easy deployment and management.

---

## How It Works

### Frontend
- **Technology**: HTML, CSS, and JavaScript.
- **Hosted On**: A Docker container using `nginx`.
- **Purpose**: Provides an intuitive user interface for selecting countries and displaying time.
- **Searchable Dropdown**: The dropdown menu includes a search bar for selecting countries or capitals.

### Backend
- **Technology**: Python with Flask.
- **Hosted On**: A Docker container.
- **Purpose**: Provides time zone data and handles API requests from the frontend.
- **CORS Enabled**: Configured to allow requests from the frontend container's origin.
- **API Endpoint**: `/time?country=<country_name>` returns the current time in both digital and analog formats.

### Docker Compose
- The application uses `docker-compose` to orchestrate the deployment of the **frontend** and **backend** containers.
- **Network**: Both containers communicate over a shared Docker bridge network.

---

## Project Architecture
1. **Frontend Service**: 
   - Runs on **nginx**.
   - Accessible at `http://Replace_with_Your_Frontend_IP:8081`.

2. **Backend Service**:
   - Runs on **Flask**.
   - Accessible at `http://Replace_with_Your_Backend_IP:5000`.

3. **Communication**:
   - The frontend communicates with the backend over the Docker network using the backend container name (e.g., `http://backend:5000`).

4. **Resources**:
   - Docker Images:
     - Frontend Image: `devopsritiks/world-time-app-frontend:<tag>`
     - Backend Image: `devopsritiks/world-time-app-backend:<tag>`
   - **System Requirements**: Docker and Docker Compose installed on the host machine.

---

## Deployment

### Prerequisites
1. Install Docker and Docker Compose on your server.
2. Replace Docker image tags and repository details in the `docker-compose.yml` file as needed.

### Steps
1. Clone the repository:
   ```bash
   git clone https://Replace_with_Your_GitHub_Repo_Link
   cd world-time-app
   ```
2. Start the application using Docker Compose:
   ```bash
   docker-compose up -d
   ```
3. Access the frontend at:
   ```
   http://Replace_with_Your_Frontend_IP:8081
   ```
4. Ensure the backend is running at:
   ```
   http://Replace_with_Your_Backend_IP:5000
   ```

---

## API Reference

### `GET /time`
**Query Parameters**:
- `country`: The name of the country (case-sensitive) for which to retrieve the time.

**Example Request**:
```
http://Replace_with_Your_Backend_IP:5000/time?country=India
```

**Response**:
```json
{
    "country": "India",
    "capital": "Asia/Kolkata",
    "digital_time": "10:30 AM",
    "analog_time": "10:30:45"
}
```

---

## Additional Notes
- Ensure the frontend communicates with the backend using the proper Docker bridge network setup.
- To add or update country data, modify the `country_capitals` dictionary in `app.py`.
- Frontend and backend Docker images should be built and pushed to a Docker Hub repository for deployment.

---

## Credits
- **Frontend Developer**: Ritik Sharma
- **Backend Developer**: Ritik Sharma
- **Docker and Deployment**: Ritik Sharma

## Note
(Replace the default IP in the app.py and index.html file with your public ip or run as a local host for app to work.)

For further details, contact: **devopsritiks@gmail.com**
