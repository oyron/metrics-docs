# Part 3: Create Grafana dashboard

### 1: Create a copy of the default dashboard

* Open the default Grafana dashboard for your application, by using the link in the application console page
* Press share button (to the right of the dashboard title) => export
* In left menu, select Dashboards => Import => Upload JSON file
* Set a name for your dashboard. Prefix with user name, e.g. OYRON Inventory API
* Change the UID til an unique id (just change one or two random characters in UID)
* Select Folder "Monitoring Workshop"
* Press 'Import'

### 2: Remove panels not needed

* Radix monitoring (text area in upper left)
* Issue count panels (critical, high, medium, low, Issues Count History)
* CPU Quota
* Memory Quota

### 3: Select defaults and save dashboard

* Select namespace "<appname>-single"
* Select 5 minute time range
* Select 5s automatic update interval
* Save dashboard
  * Select "Save current time range as dashboard default"
  * Select "Save current variable values as dashboard default"

### 4: Test dashboard

* Run a few queries through the API test client
* Verify that your dashboard is updated accordingly (after a slight delay)

Back to slides: https://oyron.github.io/monitoring-docs/#/4/1
