# Steps

| Support |     |
| ------- | --- |
| Events  | No  |

A steps element is an array of steps with content. You can use them to step through multiple parts of a setup process.

The steps take an array of elements via `-Content`.

## Usage

To create a steps element you use [`New-PodeWebSteps`](../../../Functions/Elements/New-PodeWebSteps), and supply it an array of `-Steps` using [`New-PodeWebStep`](../../../Functions/Elements/New-PodeWebStep). The [`New-PodeWebSteps`](../../../Functions/Elements/New-PodeWebSteps), also takes a `-ScriptBlock`, this is the final scriptblock that is invoked after every other step's optional `-ScriptBlock`; the one where any main logic should be performed.

!!! note
    If you have multiple steps elements on one page, make sure the Name/IDs are unique, including the Name/IDs of all form input elements as well.

Each step you create via [`New-PodeWebStep`](../../../Functions/Elements/New-PodeWebStep) has a `-Name`, `-Content`, and an optional `-ScriptBlock`. This scriptblock lets you run validation, or other logic, on a per-step basis. If any [`Show-PodeWebValidation`](../../../Functions/Actions/Show-PodeWebValidation) calls are used, then the step will be prevented from moving forward.

For example, the below renders an element with 3 steps to set up a very basic user. The email/password performs validation in their steps, with the user being created in the main final scriptblock:

```powershell
New-PodeWebSteps -Name 'AddUser' -Steps @(
    New-PodeWebStep -Name 'Details' -Icon 'User-Plus' -Content @(
        New-PodeWebTextbox -Name 'FirstName'
        New-PodeWebTextbox -Name 'LastName'
    )
    New-PodeWebStep -Name 'Email' -Icon 'Mail' -Content @(
        New-PodeWebTextbox -Name 'Email'
    ) -ScriptBlock {
        if ($WebEvent.Data['Email'] -inotlike '*@*') {
            Show-PodeWebValidation -Name 'Email' -Message 'The email supplied is invalid'
        }
    }
    New-PodeWebStep -Name 'Password' -Icon 'Lock' -Content @(
        New-PodeWebTextbox -Name 'Password' -Type Password
    ) -ScriptBlock {
        if ($WebEvent.Data['Password'].Length -lt 8) {
            Show-PodeWebValidation -Name 'Password' -Message 'Password should be 8+ characters'
        }
    }
) -ScriptBlock {
    Show-PodeWebToast -Message "User created: $($WebEvent.Data['FirstName']) $($WebEvent.Data['LastName'])"
}
```

Which would look like below:

![steps_step_1](../../../images/steps_step_1.png)
![steps_step_2](../../../images/steps_step_2.png)
![steps_step_3](../../../images/steps_step_3.png)

### Arguments

You can pass values to the scriptblock by using the `-ArgumentList` parameter. This accepts an array of values/objects, and they are supplied as parameters to the scriptblock:

```powershell
New-PodeWebSteps -Name 'AddUser' -Steps @() -ArgumentList 'Value1', 2, $false -ScriptBlock {
    param($value1, $value2, $value3)

    # $value1 = 'Value1'
    # $value2 = 2
    # $value3 = $false
}
```
