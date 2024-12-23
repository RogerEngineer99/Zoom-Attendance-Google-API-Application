Zoom Attendance Google API Application



Overview



The Zoom Attendance Google API Application is a software solution designed to automatically record students’ names and identification numbers during Zoom meetings. By using Google Sign-In tokens, the application utilizes REST APIs to capture this data and then stores it in a file format that can be accessed and reviewed by the host.



Project Duration:



Spring 2021



Features

• Automatic Attendance Logging: Automatically records the name and Identification Number (ID) of students as they join a Zoom meeting using Google Sign-In tokens.

• Google API Integration: Uses Google Sign-In for user authentication.

• REST API: The application interacts with a REST API to handle data requests and responses.

• Data Storage: Converts recorded student names and IDs into a CSV or JSON file, which is accessible to the meeting host for review.



Components and Technologies Used

• Google API: For handling user authentication and using Google Sign-In tokens.

• Zoom API: For connecting with the Zoom platform and managing meeting data.

• REST API: For communication between the frontend and backend of the application, ensuring seamless data flow.

• File Output: The recorded data is saved into an accessible file format, such as CSV or JSON.



Libraries & Frameworks:

• Google APIs Client Library: To handle user authentication via Google Sign-In.

• Zoom API: For interacting with Zoom and fetching meeting details.

• Node.js or Go: For backend functionality to interact with the APIs.



Design & Implementation



The system architecture is designed to automate the attendance-taking process for Zoom meetings, minimizing the need for host intervention.



Flow of Data:

1. User Authentication:

• Students authenticate via Google Sign-In, and their names and ID are recorded.

2. Attendance Capture:

• Once authenticated, the system records the student’s name and corresponding identification number.

3. Data Storage:

• The recorded data is converted into a file format (CSV or JSON) that can be accessed and downloaded by the Zoom meeting host.

4. Host Access:

• The host can access the generated file and review the attendance data for the meeting.



C++ Code Example



Here’s a simple example of how C++ could be used to interact with the Zoom API and record attendance data:



#include <iostream>

#include <fstream>

#include <curl/curl.h>

#include <json/json.h>



// Function to fetch Zoom meeting participants

void fetchZoomParticipants(const std::string& zoom_api_url, const std::string& access_token) {

    CURL* curl;

    CURLcode res;

    curl_global_init(CURL_GLOBAL_DEFAULT);

    curl = curl_easy_init();



    if (curl) {

        // Set URL and headers

        struct curl_slist* headers = NULL;

        headers = curl_slist_append(headers, ("Authorization: Bearer " + access_token).c_str());

        curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);

        curl_easy_setopt(curl, CURLOPT_URL, zoom_api_url.c_str());



        // Perform the request

        res = curl_easy_perform(curl);

        

        if (res != CURLE_OK) {

            std::cerr << "Request failed: " << curl_easy_strerror(res) << std::endl;

        }



        curl_easy_cleanup(curl);

        curl_global_cleanup();

    }

}



// Function to save attendance to CSV

void saveAttendanceToCSV(const std::vector<std::string>& names, const std::vector<std::string>& ids) {

    std::ofstream file("attendance.csv");



    if (file.is_open()) {

        file << "Student Name, Identification Number\n";

        for (size_t i = 0; i < names.size(); ++i) {

            file << names[i] << ", " << ids[i] << "\n";

        }

        file.close();

    } else {

        std::cerr << "Unable to open file\n";

    }

}



int main() {

    // Sample data (this would come from Zoom API)

    std::vector<std::string> names = {"John Doe", "Jane Smith", "Mark Johnson"};

    std::vector<std::string> ids = {"12345", "67890", "11223"};



    // Save attendance data to CSV

    saveAttendanceToCSV(names, ids);



    // Assuming the Zoom API URL and access token

    std::string zoom_api_url = "https://api.zoom.us/v2/meetings/{meetingId}/participants";

    std::string access_token = "<your_zoom_api_token>";



    // Fetch Zoom participants

    fetchZoomParticipants(zoom_api_url, access_token);



    return 0;

}



Key Functions:

• fetchZoomParticipants(): Fetches the participant list from the Zoom API.

• saveAttendanceToCSV(): Saves the names and IDs to a CSV file for review by the host.



Requirements:

• libcurl for making HTTP requests.

• jsoncpp for handling JSON data (if necessary, depending on the Zoom API response).



Go Code Example



Below is an example of Go code to interact with the Zoom API and Google Sign-In for attendance tracking.



package main



import (

"encoding/json"

"fmt"

"io/ioutil"

"net/http"

"os"

)



// Struct to store participant information

type Participant struct {

Name string `json:"name"`

ID   string `json:"id"`

}



// Function to fetch Zoom meeting participants

func fetchZoomParticipants(zoomAPIURL string, accessToken string) ([]Participant, error) {

req, err := http.NewRequest("GET", zoomAPIURL, nil)

if err != nil {

return nil, err

}



req.Header.Add("Authorization", "Bearer "+accessToken)

client := &http.Client{}

resp, err := client.Do(req)

if err != nil {

return nil, err

}

defer resp.Body.Close()



if resp.StatusCode != 200 {

return nil, fmt.Errorf("failed to fetch participants, status code: %d", resp.StatusCode)

}



// Read the response body

body, err := ioutil.ReadAll(resp.Body)

if err != nil {

return nil, err

}



var participants struct {

Participants []Participant `json:"participants"`

}

err = json.Unmarshal(body, &participants)

if err != nil {

return nil, err

}



return participants.Participants, nil

}



// Function to save attendance to CSV

func saveAttendanceToCSV(participants []Participant) {

file, err := os.Create("attendance.csv")

if err != nil {

fmt.Println("Error creating file:", err)

return

}

defer file.Close()



file.WriteString("Student Name, Identification Number\n")

for _, participant := range participants {

file.WriteString(fmt.Sprintf("%s, %s\n", participant.Name, participant.ID))

}

}



func main() {

// Example Zoom API URL and Access Token

zoomAPIURL := "https://api.zoom.us/v2/meetings/{meetingId}/participants"

accessToken := "<your_zoom_api_token>"



// Fetch Zoom participants

participants, err := fetchZoomParticipants(zoomAPIURL, accessToken)

if err != nil {

fmt.Println("Error fetching participants:", err)

return

}



// Save the attendance data to CSV

saveAttendanceToCSV(participants)

}



Key Functions:

• fetchZoomParticipants(): Fetches participants from Zoom using the Zoom API.

• saveAttendanceToCSV(): Saves the participant data (name and ID) to a CSV file.



Requirements:

• Go 1.16 or higher.

• Zoom API access token for authentication.



Usage Instructions



1. Set Up Google API Credentials



To enable Google Sign-In for your application, create credentials on the Google Cloud Console:

• Go to APIs & Services > Credentials and create a new OAuth 2.0 Client ID for your project.

• Download the credentials file and configure it in your project.



2. Integrate with Zoom API



To interact with the Zoom platform:

• Sign up for a Zoom Developer Account.

• Create an app and obtain your Zoom API credentials (API Key and Secret).

• Set up the necessary endpoints to capture attendance information via the Zoom API.



3. Run the Application

1. Clone this repository to your local machine.

2. Install any necessary dependencies for the C++ or Go backend.

3. Set up the necessary environment variables for Google and Zoom API keys.

4. Run the application.



After the Zoom meeting ends, the attendance file will be available for the host to download and review.



Future Enhancements

• Real-time Attendance Reporting: Update the file in real-time as students join the Zoom meeting.

• Integration with Google Sheets: Automatically send the attendance data to a Google Sheet for better tracking.

• Host Dashboard: Build a user-friendly dashboard to allow hosts to customize attendance reports.



License



This project is licensed under the MIT License. See the LICENSE file for more details.



This updated README.md now includes both C++ and Go code examples that demonstrate how to integrate with the Zoom API and manage attendance tracking. 
