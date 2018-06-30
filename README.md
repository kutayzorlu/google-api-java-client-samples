# Samples of using the [Google APIs Client Library for Java](https://github.com/google/google-api-java-client)
For a complete list of Google APIs, visit https://developers.google.com/api-client-library/java/apis/

Java Quickstart
Complete the steps described in the rest of this page, and in about five minutes you'll have a simple Java command-line application that makes requests to the Drive API.

Prerequisites
To run this quickstart, you'll need:

Java 1.7 or greater.
Gradle 2.3 or greater.
A Google account with Google Drive enabled.
Step 1: Turn on the Drive API
Use this wizard to create or select a project in the Google Developers Console and automatically turn on the API. Click Continue, then Go to credentials.
On the Add credentials to your project page, click the Cancel button.
At the top of the page, select the OAuth consent screen tab. Select an Email address, enter a Product name if not already set, and click the Save button.
Select the Credentials tab, click the Create credentials button and select OAuth client ID.
Select the application type Other, enter the name "Drive API Quickstart", and click the Create button.
Click OK to dismiss the resulting dialog.
Click the file_download (Download JSON) button to the right of the client ID.
Move this file to your working directory and rename it client_secret.json.
Step 2: Prepare the project
In your working directory, run the following commands to create a new project structure:

gradle init --type basic
mkdir -p src/main/java src/main/resources 
Copy the client_secret.json file you downloaded in Step 1 into the src/main/resources/ directory you just created.

Open the default build.gradle file and replace its contents with the following code:
drive/quickstart/build.gradleVIEW ON GITHUB
apply plugin: 'java'
apply plugin: 'application'

mainClassName = 'DriveQuickstart'
sourceCompatibility = 1.7
targetCompatibility = 1.7
version = '1.0'

repositories {
    mavenCentral()
}

dependencies {
    compile 'com.google.api-client:google-api-client:1.23.0'
    compile 'com.google.oauth-client:google-oauth-client-jetty:1.23.0'
    compile 'com.google.apis:google-api-services-drive:v3-rev110-1.23.0'
}
Step 3: Set up the sample
Create a file in the src/main/java/ folder with the following filename and code:

drive/quickstart/src/main/java/DriveQuickstart.javaVIEW ON GITHUB
import com.google.api.client.auth.oauth2.Credential;
import com.google.api.client.extensions.java6.auth.oauth2.AuthorizationCodeInstalledApp;
import com.google.api.client.extensions.jetty.auth.oauth2.LocalServerReceiver;
import com.google.api.client.googleapis.auth.oauth2.GoogleAuthorizationCodeFlow;
import com.google.api.client.googleapis.auth.oauth2.GoogleClientSecrets;
import com.google.api.client.googleapis.javanet.GoogleNetHttpTransport;
import com.google.api.client.http.javanet.NetHttpTransport;
import com.google.api.client.json.JsonFactory;
import com.google.api.client.json.jackson2.JacksonFactory;
import com.google.api.client.util.store.FileDataStoreFactory;
import com.google.api.services.drive.Drive;
import com.google.api.services.drive.DriveScopes;
import com.google.api.services.drive.model.File;
import com.google.api.services.drive.model.FileList;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.security.GeneralSecurityException;
import java.util.Collections;
import java.util.List;

public class DriveQuickstart {
    private static final String APPLICATION_NAME = "Google Drive API Java Quickstart";
    private static final JsonFactory JSON_FACTORY = JacksonFactory.getDefaultInstance();
    private static final String CREDENTIALS_FOLDER = "credentials"; // Directory to store user credentials.

    /**
     * Global instance of the scopes required by this quickstart.
     * If modifying these scopes, delete your previously saved credentials/ folder.
     */
    private static final List<String> SCOPES = Collections.singletonList(DriveScopes.DRIVE_METADATA_READONLY);
    private static final String CLIENT_SECRET_DIR = "client_secret.json";

    /**
     * Creates an authorized Credential object.
     * @param HTTP_TRANSPORT The network HTTP Transport.
     * @return An authorized Credential object.
     * @throws IOException If there is no client_secret.
     */
    private static Credential getCredentials(final NetHttpTransport HTTP_TRANSPORT) throws IOException {
        // Load client secrets.
        InputStream in = DriveQuickstart.class.getResourceAsStream(CLIENT_SECRET_DIR);
        GoogleClientSecrets clientSecrets = GoogleClientSecrets.load(JSON_FACTORY, new InputStreamReader(in));

        // Build flow and trigger user authorization request.
        GoogleAuthorizationCodeFlow flow = new GoogleAuthorizationCodeFlow.Builder(
                HTTP_TRANSPORT, JSON_FACTORY, clientSecrets, SCOPES)
                .setDataStoreFactory(new FileDataStoreFactory(new java.io.File(CREDENTIALS_FOLDER)))
                .setAccessType("offline")
                .build();
        return new AuthorizationCodeInstalledApp(flow, new LocalServerReceiver()).authorize("user");
    }

    public static void main(String... args) throws IOException, GeneralSecurityException {
        // Build a new authorized API client service.
        final NetHttpTransport HTTP_TRANSPORT = GoogleNetHttpTransport.newTrustedTransport();
        Drive service = new Drive.Builder(HTTP_TRANSPORT, JSON_FACTORY, getCredentials(HTTP_TRANSPORT))
                .setApplicationName(APPLICATION_NAME)
                .build();

        // Print the names and IDs for up to 10 files.
        FileList result = service.files().list()
                .setPageSize(10)
                .setFields("nextPageToken, files(id, name)")
                .execute();
        List<File> files = result.getFiles();
        if (files == null || files.isEmpty()) {
            System.out.println("No files found.");
        } else {
            System.out.println("Files:");
            for (File file : files) {
                System.out.printf("%s (%s)\n", file.getName(), file.getId());
            }
        }
    }
}
Step 4: Run the sample
Build and run the quickstart with the command:

gradle -q run
The first time you run the sample, it will prompt you to authorize access:

The sample will attempt to open a new window or tab in your default browser. If this fails, copy the URL from the console and manually open it in your browser.

If you are not already logged into your Google account, you will be prompted to log in. If you are logged into multiple Google accounts, you will be asked to select one account to use for the authorization.

Click the Accept button.
The sample will proceed automatically, and you may close the window/tab.
IT WORKED!
I GOT AN ERROR
Notes
Authorization information is stored on the file system, so subsequent executions will not prompt for authorization.
The authorization flow in this example is designed for a command-line application. For information on how to perform authorization in a web application, see Using OAuth 2.0 for Web Server Applications.
Further reading
Google Developers Console help documentation
Google APIs Client for Java documentation
Drive API Javadoc documentation
Drive REST API reference documentation
Except as otherwise noted, the content of this page is licensed under the Creative Commons Attribution 3.0 License, and code samples are licensed under the Apache 2.0 License. For details, see our Site Policies. Java is a registered trademark of Oracle and/or its affiliates.

Zuletzt aktualisiert: Juni 14, 2018


# Drive API Client Library for Java
 Drive API: Manages files in Drive including uploading, downloading, searching, detecting changes, and updating sharing permissions.

This page contains information about getting started with the Drive API using the Google API Client Library for Java. In addition, you may be interested in the following documentation:

Browse the JavaDoc reference for the Drive API.
Read the Developer's Guide for the Google API Client Library for Java.
Interact with this API in your browser using the APIs Explorer for the Drive API.
Sample
The drive-cmdline-sample may help you get started using the client library.

Add Library to Your Project
Select your build environment (Maven or Gradle) from the following tabs, or download a zip file containing all of the jars you need:

DOWNLOADMAVENGRADLE
 Download the Drive API v3 Client Library for Java.

See the drive/readme.html file for details on:

What the zip file contains.
Which dependent jars are needed for each application type (web, installed, or Android application).
The libs folder contains all the of the globally applicable dependencies you might need across all application types.

# Uploading Files
The Drive API allows you to upload file data when creating or updating a File resource.

You can send upload requests in any of the following ways:

Simple upload: uploadType=media. For quick transfer of a small file (5 MB or less). To perform a simple upload, refer to Performing a Simple Upload.
Multipart upload: uploadType=multipart. For quick transfer of a small file (5 MB or less) and metadata describing the file, all in a single request. To perform a multipart upload, refer to Performing a Multipart Upload.
Resumable upload: uploadType=resumable. For more reliable transfer, especially important with large files. Resumable uploads are a good choice for most applications, since they also work for small files at the cost of one additional HTTP request per upload. To perform a resumable upload, refer to Performing a Resumable Upload.
Most Google API client libraries implement at least one of the methods. Refer to the client library documentation for additional details on how to use each of the methods.

        File fileMetadata = new File();
        fileMetadata.setName("photo.jpg");
        java.io.File filePath = new java.io.File("files/photo.jpg");
        FileContent mediaContent = new FileContent("image/jpeg", filePath);
        File file = driveService.files().create(fileMetadata, mediaContent)
            .setFields("id")
            .execute();
        System.out.println("File ID: " + file.getId());

## Importing to Google Docs types
    File fileMetadata = new File();
    fileMetadata.setName("My Report");
    fileMetadata.setMimeType("application/vnd.google-apps.spreadsheet");

    java.io.File filePath = new java.io.File("files/report.csv");
    FileContent mediaContent = new FileContent("text/csv", filePath);
    File file = driveService.files().create(fileMetadata, mediaContent)
        .setFields("id")
        .execute();
    System.out.println("File ID: " + file.getId());


From	To
Microsoft Word, OpenDocument Text, HTML, RTF, plain text	Google Docs
Microsoft Excel, OpenDocument Spreadsheet, CSV, TSV, plain text	Google Sheets
Microsoft Powerpoint, OpenDocument Presentation	Google Slides
JPEG, PNG, GIF, BMP, PDF	Google Docs (embeds the image in a Doc)
plain text (special MIME type), JSON	Google Apps Script
When uploading and converting media during an update request to a Google Doc, Sheet, or Slide the full contents of the document will be replaced.

When converting images you can improve the quality of the OCR algorithm by specifying the applicable BCP 47 language code in the ocrLanguage parameter. The extracted text will appear in the Google Docs document alongside the embedded image.

Uploading using a pregenerated ID
The Drive API allows you to retrieve a list of pregenerated file IDs that can be used for uploading and creating resources. Upload and file creation requests can then include these pregenerated IDs by setting the id fields in the file metadata.

You can safely retry uploads with pregenerated IDs in the case of an indeterminate server error or timeout. If the file was successfully created, subsequent retries return a HTTP 409 error instead of creating duplicate files.


