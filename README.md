<a href="https://githubsfdeploy.herokuapp.com">
  <img alt="Deploy to Salesforce"
       src="https://raw.githubusercontent.com/afawcett/githubsfdeploy/master/deploy.png">
</a>

# LWCAction
To use LWC as Lightning Action we need to wrap it with Aura Component. To avoid creating new not needed Aura Components, coding close Lightning Action event handler, adding spinners, we can create generic Aura Component which will dynamically create LWC depending on Lighting Action API Name.

### How to use
1. In LWC component's '.js-meta.xml' file update 'isExposed' field:
```
<isExposed>true</isExposed>
```
2. Lightning Action should invoke Aura Component 'LWCLightningAction' / 'LWCLightningActionMedium' / 'LWCLightningActionHuge' depending on size.
3. Lightning Action API Name should be same as LWC API Name. I can advise naming LWC as SObjectName + ActionName + Action, example: quoteApplyDiscountAction.
4. LWC should extend 'LwcAction':
```
import LwcAction from 'c/lwcAction'
export default class AccountForecastManagerAction extends LwcAction {}
```
5. When LWC is ready to show content (data is loaded usually), call:
```
this.ready()
```

### Features
- For closing action from LWC call: 
```
this.closeAction()
```

- To show spinner:
```
this.showSpinner()
```

- To hide spinner:
```
this.hideSpinner()
```

- Id of record from which Lighting Action was invoked is passed to LWC and can be accessed using:
```
this.recordId
```

### Additional Features
- To show Toast Message call 'showToast(title, message, variant)':
```
this.showToast('title', 'message', 'info')
```

- To simplify wire service use 'handleWire', example:
```
@wire (getData, {accountId: '$recordId', quartersCount: '$quartersCount'}) wireData (value) {
        this.wiredData = value
        this.handleWire(value, data => {
            if (data.opportunities.length == 0) {
                throw 'No opportunities'
            }
            this.initFilters(data.opportunities)
        })
    }
```
If 'value.error' is not null or passed callback throw error, the Lighting Action will be closed and error will be shown with Toast Message.

- You can hide component before it has loaded all necessary data by wrapping it with:
```
<template if:true={isReady}></template>
```
Example:
```
<template>
  <template if:true={isReady}>
    <div>Your component</div>
  </template>
<template>
```
And calling when needed:
```
this.ready()
```
This method also hides spinner.
