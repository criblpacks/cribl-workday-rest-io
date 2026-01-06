# Cribl Workday Rest Collector
----
## About this Pack

This pack is built as a complete SOURCE + DESTINATION solution (identified by the IO suffix). Data collection and delivery happen entirely within the pack's context, eliminating the need to connect it to globally defined Sources and Destinations. 

This Pack is designed to collect, process, and output Workday Activity Log Event data using the [Workday REST API](https://community.workday.com/sites/default/files/file-hosting/restapi/#privacy/v1/get-/activityLogging). 

The Pack includes optional Splunk output processing that maps data to Splunk sourcetypes.

## Deployment

* This pack is configured by default to use the Worker Group's *Default Destination*.
* To use the *Default Destination*: No changes are required. The pack will route the data to the destination currently set as the Default on the Worker Group.
* To use a different Destination: You must update the pack's routes to specify your desired Destination.
* For immediate functionality without requiring Pack route filter expression modifications, every bundled Source within this pack adds a hidden field: `__packsource`. This field allows for seamless routing based on the Pack source.

### Configure Workday
1. Create an Integration System User

- Access the Create Integration System User task and provide the following parameters:
    - User Name: cribl_ISU.
    - New Password and New Password Verify: Enter the password.
    - Do Not Allow UI Sessions: Check the box.
    - Session Timeout Minutes: 0 (Disable session expiration).
- Click OK.
    - Exempt the created user from the password expiration rule.
    - Access Maintain Password Rules task.
    - Add the users to System Users exempt from password expiration.

2. Create a Security Group

- To create a security group, access the Create Security Group task and provide the following parameters:
    - Type of Tenanted Security Group. Integration System Security Group (Unconstrained)
    - Name. Cribl Client Security Group.
- Click OK.
- In the Edit Integration System Security Group (Unconstrained) window provide the following parameters:
    - Integration System Users. cribl_ISU.
    - Comment (Optional). Provide a short description.
- Click OK.
- To attach the security group to a domain, access the View Domain task for the domain System Auditing.
- Select Domain > Edit Security Policy Permissions from the System Auditing related Actions menu.
- Add the Cribl Client Security Group you created to both the tables as below:
    - Report/Task Permissions table. View access.
    - Integration Permissions table. Get access.
- Click OK.
- To apply policy changes, access the Activate Pending Security Policy Changes task and activate the changes you made.
- Click OK.

3. Register the API Client

- To register the API client, access the Register API Client for Integrations task, and provide the following parameters:
    - Client Name. Cribl Workday Collector
    - Non-Expiring Refresh Tokens. Yes.
    - Scope. System.
- Click OK.
- Copy the Client Secret and Client ID before you navigate away from the page and store it securely. If you lose the Client Secret, you can generate a new one using the Generate New API Client Secret task.
- Click Done.
- To generate a refresh token, access the View API Clients task and copy the below two parameters from the top of the page:
    - Workday REST API Endpoint. The endpoint to use access to the resources in your Tenant.
    - Token Endpoint. The endpoint used to exchange an authorization code for a token (if you configure authorization code grant).
- Go to the API Clients for Integrations tab, hover on the "Cribl Workday Collector API" client, and click on the three-dot kebab action buttons.
- In the new pop up window, click API Client > Manage Refresh Token for Integrations.
- In the Manage Refresh Token for Integrations window, select "cribl_ISU" in the Workday Account field and click OK.
- In the newly opened window, select Generate New Refresh Token checkbox and click OK.
- Copy the value of the Refresh Token column from the opened window and click Done.

### Configure the Collectors

#### Variables

The Pack has the following variables:
* `workday_domain`: company specific workday subdomain
* `workday_customer_dir`: Provided by WorkDay. Location of your services.
* `workday_client_id`: Created above when configuring workday
* `workday_refresh_token`: Created above when configuring workday
* `workday_default_splunk_index`: Default index for the Splunk output.

#### Source Configuration

In addition to configuring variables you will also need to configure the `client_secret` within the collector source

* Navigate to Sources > Collectors > REST > workday-activity-logging > Authentication > Client secret value and replace the placeholder value with your Client Secret created from steps above.
* Perform a Commit and Deploy - this ensures that Preview works correctly. 
* Perform a **Run > Preview** of each Collector to verify that they work correctly.
* Schedule the Collectors and adjust the schedule as needed. 

### Configure Output Format

Each data type can be configured to output data in either normalized JSON or Splunk (`_raw` + Splunk fields) format. Enable *only one* format for each of the following pipelines:
* `cribl-workday`

### Configure your Destination/Update Pack Routes
To ensure proper data routing, you must make a choice: retain the current setting to use the Default Destination defined by your Worker Group, or define a new Destination directly inside this pack and adjust the pack's routes accordingly.

### Commit and Deploy
Once everything is configured, perform a Commit & Deploy to enable data collection.

## Release Notes

### Version 1.0.0
- Initial release

## Contributing to the Pack

To contribute to the Pack, please connect with us on [Cribl Community Slack](https://cribl-community.slack.com/). You can suggest new features or offer to collaborate.

## License
This Pack uses the following license: [Apache 2.0](https://github.com/criblio/appscope/blob/master/LICENSE).