A collection of selected values when the control is set to multiselect mode.

You can check for the number of selected values like this:

```foxpro
IF this.lstChoices.Count > 0
	FOR lnX = 1 to this.lstChoices.Count
		lcOutput = lcOutput + this.lstChoices.SelectedValues.Item(lnX) + "<hr>"
	ENDFOR
	this.lblMessage.Text = lcOutput
ENDIF
```

Note that SelectedValues is used only if the control is in MultiSelect mode. Otherwise use [SelectedValue](vfps://Topic/_1MX0QL915) to retrieve selection status. SelectedValue is always set even on multiple selections in which case the first value is the SelectedValue.