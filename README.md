# Technical Guidance

## converter.py Code Explanation

This module implements the core coordinate conversion functionality of the application. Its primary purpose is to convert British National Grid (BNG) coordinates, entered either as Easting/Northing values or as an Ordnance Survey Grid Reference, into WGS84 latitude and longitude coordinates. The converted coordinates can then be visualised on an interactive map and exported as a CSV file.

The code and subsequent application uses the ‘pyproj’ library to perform accurate coordinate system transformations. A ‘Transformer’ object is initialised to convert coordinates from the British National Grid coordinate reference system (EPSG:27700) to the global WGS84 coordinate reference system (EPSG:4326).

To support Ordnance Survey Grid References, the application defines a two-dimensional ‘GRID’ array that represents the official 100 km grid squares covering Great Britain - given each grid reference begins with letters. The ‘gridref_to_en()’ function parses a user-provided grid reference, identifies the corresponding grid square, separates the numeric easting and northing components, and converts them into full Easting and Northing coordinates measured in metres. The function contains validation checks and returns an error message if certain conditions are not met: 1) grid reference too short; 2) non-numeric digits; 3) incorrect grid reference letters; 4) odd number of digits; 5) empty numeric section.

The ‘easting_northing_to_latlon()’ function performs the coordinate transformation by passing the Easting and Northing values to the ‘pyproj’ transformer and returning the equivalent latitude and longitude coordinates.

The main processing logic is contained within the ‘convert()’ function, which acts as the application's central controller. It determines whether the user has chosen to convert from Easting/Northing coordinates or an Ordnance Survey Grid Reference, validates the input, performs the appropriate conversion, and returns the result as formatted latitude and longitude values. The function also includes exception handling to provide informative error messages for invalid inputs or unexpected failures, improving the robustness of the application.

To improve usability, the ‘generate_map()’ function uses the ‘Folium’ library to create an interactive map centred on the converted location. A marker is placed at the calculated coordinates, allowing users to visually confirm the conversion results within the application's interface.

Finally, the ‘export_csv()’ function enables users to save their conversion results. The function stores the conversion mode, user inputs and resulting coordinates in a Pandas DataFrame before writing the data to a temporary CSV file, which can then be downloaded by the user. This provides a simple mechanism for recording and sharing conversion results.

Overall, this module separates the application's core responsibilities into distinct functions responsible for coordinate conversion, input validation, map visualisation and data export, making the code easier to maintain, test and extend. The file can subsequently be downloaded by the user for use.

## app.py Code Explanation

This module implements the graphical user interface (GUI) for the application using the Gradio framework. Rather than performing coordinate conversions directly, it provides the interface through which users enter data, view results, generate maps and export conversion data. The module imports the ‘convert()’, ‘generate_map()’ and ‘export_csv()’ functions from the ‘converter.py’ module described above, separating the application's presentation layer from its processing logic.

The interface is created using ‘gr.Blocks()’, which acts as the main container for all interface components. A title and brief description are displayed using Markdown, while a ‘gr.State’ object is used to temporarily store the user's most recent successful conversion. This stored data allows the conversion results to be exported later without requiring the user to perform the conversion again.

The interface is organised into three tabs. The ‘Home’ tab provides a brief introduction to the application, the ‘Converter’ tab contains the main conversion functionality, and the ‘Export CSV’ tab enables users to download the most recent conversion result.

Within the Converter tab, users first select the input format using a radio button that allows either Easting/Northing coordinates or an Ordnance Survey Grid Reference to be entered. Depending on the selected option, users provide either numerical Easting and Northing values or a grid reference before pressing the ‘Convert’ button to initiate the conversion process.

The ‘run_convert() function acts as the event handler for the Convert button. It passes the user inputs to the ‘convert()’ function contained within the ‘converter.py’ module and receives either a successful conversion result or an error message. If the returned value does not begin with ‘Latitude’, the function assumes the conversion has failed and returns the error message without attempting to generate a map.

When a valid conversion is returned, the function extracts the latitude and longitude values from the formatted output string. These coordinates are then passed to the ‘generate_map()’ function, which creates an interactive Folium map centred on the converted location. Finally, the function returns the conversion result, the generated map and a dictionary containing the user's inputs and results. This dictionary is stored in the application's state object so that it can be accessed later by the export functionality.

The Export CSV tab contains an ‘Export Last Result’ button and a downloadable file component. When the export button is pressed, the ‘do_export()’ function retrieves the stored conversion data from the application state. If no previous conversion exists, the function returns no file. Otherwise, it passes the stored values to the ‘export_csv()’ function, which generates a CSV file containing the conversion details and makes it available for download.

Finally, the application is started using ‘demo.launch()’, which launches the Gradio web server and makes the user interface accessible through a web browser.



## How to run the application locally and run tests


### Step 1: Download the Repository

- Navigate to the GitHub repository: https://github.com/JoeT23/Location_Converter
- Click ‘Code’ and select ‘Download ZIP.
- Extract the downloaded ZIP file to a convenient location on your computer.

### Step 2: Open Command Prompt

- Open Command Prompt by searching for ‘cd’ in the Search bar.

### Step 3: Navigate to the Project Folder where you have stored the extracted download

- Use the `cd` (change directory) command to move to the folder containing the project files. For example, if the project was extracted to the Downloads folder: cd \Downloads\Location_Converter-main
- Alternatively, if it was extracted elsewhere, replace the path with the location of the extracted project folder.

### Step 4: Verify You Are in the Correct Folder

- Type and return: dir
- The output should display the project files, including: app.py; converter.py; requirements.txt; README.md (image below)

### Step 5: Install the Required Dependencies

- Install the required Python packages by running: pip install -r requirements.txt

### Step 6: Launch the Application

- Start the application by running: python app.py
- Once the application starts, the terminal will display a local web address similar to image below.


### Step 7: Open the Application

- Open the displayed URL in a web browser to access the application locally.


## How to run tests (unit tests) locally

To execute the unit tests:

- Open Command Prompt and navigate to the project directory (as above).
- Ensure all project dependencies have been installed (as above).
- Run the following command: python -m pytest tests

Pytest will automatically discover and execute all unit test cases contained within the `tests` directory. A successful test should run. This confirms that all unit tests have passed successfully and that the application's core conversion functions are operating as expected.


