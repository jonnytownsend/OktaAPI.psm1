# OktaAPI.psm1
Call Okta API from PowerShell -- unofficial code.

This module provides a very thin wrapper around the [Okta API](https://developer.okta.com/documentation/). It converts to/from JSON. It supports [pagination](https://developer.okta.com/docs/api/getting_started/design_principles#pagination) of objects and allows you to check [rate limits](https://developer.okta.com/docs/api/getting_started/rate-limits).

It assumes you are familiar with the Okta API and using REST.

# Contents
- [Usage](#usage)
- [Installation](#installation)
- [Converting JSON to PowerShell](#converting-json-to-powershell)
- [Adding new endpoints](#adding-new-endpoints)

# Usage
```powershell
Connect-Okta "YOUR_API_TOKEN" "https://YOUR_ORG.oktapreview.com"

$user = Get-OktaUser "me"
$group = Get-OktaGroups "PowerShell" 'type eq "OKTA_GROUP"'
Add-OktaGroupMember $group.id $user.id
```

See [CallOktaAPI.ps1](CallOktaAPI.ps1) for more samples.

# Installation
To determine which version of PowerShell you're running, see PSVersion under `$PSVersionTable`.

**To Install on PowerShell 5 or newer**

```powershell
Install-Module OktaAPI # [1]

Install-Script CallOktaAPI # [2]
```
CallOktaAPI.ps1 has sample code. Replace `YOUR_API_TOKEN` and `YOUR_ORG` with your values or use OktaAPISettings.ps1.

[1] https://www.powershellgallery.com/packages/OktaAPI <br>
[2] https://www.powershellgallery.com/packages/CallOktaAPI

**To Install on PowerShell 4 or older**

1. `$env:PSModulePath` contains a list of folders where modules live (e.g., C:\Users\Administrator\Documents\WindowsPowerShell\Modules). 
Create a new folder in a folder in your module path called OktaAPI (e.g., C:\Users\Administrator\Documents\WindowsPowerShell\Modules\OktaAPI).
2. Copy OktaAPI.psm1 to the new folder: Modules\OktaAPI
3. Copy CallOktaAPI.ps1. It has sample code. Replace `YOUR_API_TOKEN` and `YOUR_ORG` with your values or use OktaAPISettings.ps1.

# Converting JSON to PowerShell
Most Okta API calls come with sample `curl` commands with blocks of JSON. To convert from JSON to PowerShell:
* Change `{` to `@{`
* Change `:` to `=`
* Change `,` to `;` or use a line break instead
* Change `[` to `@(`, and `]` to `)`
* Change `true`, `false` and `null` to `$true`, `$false` and `$null`

Here is an example from [Assign User to App](https://developer.okta.com/docs/api/resources/apps#assign-user-to-application-for-sso):

**JSON**
```json
{
  "id": "00ud4tVDDXYVKPXKVLCO",
  "scope": "USER",
  "credentials": {
    "userName": "user@example.com",
    "password": {
      "value": "correcthorsebatterystaple"
    }
  }
}
```

**PowerShell**
```powershell
@{
  id = "00ud4tVDDXYVKPXKVLCO"
  scope = "USER"
  credentials = @{
    userName = "user@example.com"
    password = @{
      value = "correcthorsebatterystaple"
    }
  }
}
```

# Adding new endpoints
To add a new endpoint, check the documentation for the [HTTP verb](https://developer.okta.com/docs/api/getting_started/design_principles#http-verbs) (e.g. `GET`, `POST`, `PUT`, `DELETE`) and URL, and convert it into a corresponding PowerShell call.

For example, the documentation for [Get User](https://developer.okta.com/docs/api/resources/users#get-user) says:
```
GET /api/v1/users/${id}
```

The PowerShell code is:
```powershell
function Get-OktaUser($id) {
    Invoke-Method GET "/api/v1/users/$id"
}
```

See [Modules/OktaAPI.psm1](Modules/OktaAPI.psm1) for more examples.
