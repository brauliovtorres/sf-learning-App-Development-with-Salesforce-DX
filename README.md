# App Development with Salesforce DX

[Use the Salesforce command-line interface to create, convert, and deploy apps.](https://trailhead.salesforce.com/en/content/learn/modules/sfdx_app_dev)

## Hands-On
- To get started, log in to the Dev Hub using the CLI, so you’re authorized to create scratch orgs. `sfdx auth:web:login`
- To authorize the Dev Hub, use the web login flow: `sfdx auth:web:login -d -a <your-devhub-alias>`
- Adding the `-d` flag sets this org as the default Dev Hub. Use the `-a` flag to set an alias for the org (something catchy like DevHub).

### Create a Scratch Org
- In a command window, navigate to where you want your project located.
- Create the project: `sfdx force:project:create -n <your-project-name>`
- In the command window, change to the `<your-project-name>` project directory.
- Create a scratch org with the alias <your-scratchorg-alias> `sfdx force:org:create -s -f config/project-scratch-def.json -a <your-scratchorg-alias>`
- The `-s` option indicates that you want this scratch org to be the default org for this project when running Salesforce CLI commands. To use a different org on a per command basis, you can specify the `-u` argument and specify another alias.
- The `-f` option is the path to the project scratch org configuration file.
- The `-a` option refer to the org using its alias.

### Create Sample Data
- Create the Marriott Marquis account.
  `sfdx force:data:record:create -s Account -v "Name='Marriott Marquis' BillingStreet='780 Mission St' BillingCity='San Francisco' BillingState='CA' BillingPostalCode='94103' Phone='(415) 896-1600' Website='www.marriott.com'"`

- Create the Hilton Union Square account.
  `sfdx force:data:record:create -s Account -v "Name='Hilton Union Square' BillingStreet='333 O Farrell St' BillingCity='San Francisco' BillingState='CA' BillingPostalCode='94102' Phone='(415) 771-1400' Website='www.hilton.com'"`

- Create the Hyatt account.
  `sfdx force:data:record:create -s Account -v "Name='Hyatt' BillingStreet='5 Embarcadero Center' BillingCity='San Francisco' BillingState='CA' BillingPostalCode='94111' Phone='(415) 788-1234' Website='www.hyatt.com'"`

- In your Salesforce DX project, create a directory called `data`
  `mkdir data`

- Export some sample data.
  `sfdx force:data:tree:export -q "SELECT Name, BillingStreet, BillingCity, BillingState, BillingPostalCode, Phone, Website FROM Account WHERE BillingStreet != NULL AND BillingCity != NULL and BillingState != NULL" -d ./data`

### Create the Account Search Apex Controller
- From within the `<your-project>` directory, run this command from the root of your project: `sfdx force:apex:class:create -n AccountSearchController -d force-app/main/default/classes`
- Open `AccountSearchController.cls` and replace the scaffold code with this code, then save the file.
- Now, push (synchronize) your new code to the scratch org: `sfdx force:source:push`

### Create the Accounts Loaded Aura Event
- Create the event in the aura folder: `sfdx force:lightning:event:create -n AccountsLoaded -d force-app/main/default/aura`
- Open `AccountsLoaded.evt`and replace the contents with this code, then save the file.
- Push your new code to the scratch org: `sfdx force:source:push`

### Create the Account Search Aura Component
- Create the component in the `aurafolder`: `sfdx force:lightning:component:create -n AccountSearch -d force-app/main/default/aura`
- Open `AccountSearch.cmp` and replace its contents with the following code, then save the file.
- Open `AccountSearchController.js`and replace its contents with the following code, then save the file.
- Open `AccountSearchHelper.js`and replace its contents with the following code, then save the file.
- Push your new code to the scratch org: `sfdx force:source:push`

### Create the Account List Aura Component
- Create the component in the `aura` folder: `sfdx force:lightning:component:create -n AccountList -d force-app/main/default/aura`
- Open `AccountList.cmp` and replace its contents with the following code, then save the file.
- Open `AccountListController.js` and replace its contents with the following code, then save the file.
- Push your new code to the scratch org: `sfdx force:source:push`

### Create the Account Map Aura Component
- Create the AccountMap component in the `aura` folder: `sfdx force:lightning:component:create -n AccountMap -d force-app/main/default/aura`
- Open `AccountMap.cmp` and replace its contents with the following code, then save the file.
- Open `AccountMapController.js` and replace its contents with the following code, then save the file.
- Push your new code to the scratch org: `sfdx force:source:push`

### Create the Account Locator Aura Component
- Create the AccountMap component in the `aura` folder: `sfdx force:lightning:component:create -n AccountLocator -d force-app/main/default/aura`
- Open `AccountLocator.cmp` and replace its contents with the following code, then save the file.
- Push your new code to the scratch org: `sfdx force:source:push`

### Create the Account Locator Custom Tab
- Open your scratch org: `sfdx force:org:open`
- From Setup, enter `Tabs` in the Quick Find box, then click Tabs.
- In the Lightning Component Tabs section, click New and enter the details:
  - Lightning Component: c:AccountLocator
  - Tab Label: Account Locator
  - Tab Name: Account_Locator
  - Tab Style: Click the magnifier icon and select Map as the tab icon.
  - Description: Custom tab for Account Locator.
  - Click Next.
  - For Apply one tab visibility to all profiles, choose Tab Hidden.
  - Click Save.

### Create the Geolocation Permission Set
- From Setup, enter Permission Sets in the Quick Find box, then click Permission Sets.
- Click New and enter the details:
  - Label: Geolocation
  - API Name: Geolocation
  - Description: Grants access to the Account Geolocation app
- Click Save.
- Under Apps, click Object Settings.
- Click Account Locator.
- Click Edit.
- For Tab Settings, select both Available and Visible, then click Save.
- Click Manage Assignments, then Add Assignments.
- Select the checkbox next to the scratch org username (User, User), click Assign, then click Done.

### Metadata Magic: Pull Changes into Your Project
- In the root directory of your geolocation project, open the `.forceignore` file and add `**/profiles` as a new line to the file, then save the file.
- In the command window, sync the changes you made in the scratch org with your local project: `sfdx force:source:pull`

### Validate Your App
- Create a new scratch org: `sfdx force:org:create -f config/project-scratch-def.json -a <your-test-org>`
- Push your local source and metadata to the scratch org: `sfdx force:source:push -u <your-test-org>`
- Assign your permission set: `sfdx force:user:permset:assign -n Geolocation -u <your-test-org>`
- Load your sample data into the org. This is the data you exported from a different scratch org in a previous unit: `sfdx force:data:tree:import -f data/Account.json -u <your-test-org>`
- Open your org: `sfdx force:org:open -u <your-test-org>`
- Test the Account Locator tab, and verify that it works as expected. From the App Launcher, find and select Account Locator.

### Delete and Logout
- `sfdx force:org:delete -u <your-org-alias>`
- `sfdx auth:logout --all`
