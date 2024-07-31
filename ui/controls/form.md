---
title: "Controls: Form"
---
# Form

The form is the base element of data entry within Winter CMS. A form is made up of controls, which can be input fields or widgets. In general, the form is generally created by the [Form Widget](../../backend/forms), but can also be manually created for custom uses.

## The basics

A form should be made up of a `<form>` tag. Each individual input or control within the form should be surrounded by a `<div class="form-group">` container, similar to the [Bootstrap v4](https://getbootstrap.com/docs/4.6/components/forms/) format.

Within each form group, there should be a `<label>` tag for the field label, and a control or input that has the class `form-control` to identify the actual interactive element. For usability, you should endeavour to include a `for` attribute on the label that matches the `name` attribute of the field that label is for.

```html
<form>
    <div class="form-group">
        <label for="name">Your name</label>
        <input type="text" name="name" value="" class="form-control">
    </div>
</form>
```

```backend
<form>
    <div class="form-group">
        <label for="name">Your name</label>
        <input type="text" name="name" value="" class="form-control">
    </div>
</form>
```

## Alignment

Form fields can be spanned on either the left or right half of the page, or across the entire page, providing a simple way of organising fields. Simply add the `span-left` class to the `form-group` container for a field you wish to align to the left half of the form, `span-right` for the right half, or `span-full` (or no class) to span the entire width of the form.

```html
<form class="form-elements">
    <div class="form-group span-left">
        <label>First name</label>
        <input type="text" name="" value="" class="form-control">
    </div>

    <div class="form-group span-right">
        <label>Last name</label>
        <input type="text" name="" value="" class="form-control">
    </div>

    <div class="form-group span-full">
        <label>Address</label>
        <input type="text" name="" value="" class="form-control">
    </div>
</form>
```

```backend
<form class="form-elements">
    <div class="form-group span-left">
        <label>First name</label>
        <input type="text" name="" value="" class="form-control">
    </div>

    <div class="form-group span-right">
        <label>Last name</label>
        <input type="text" name="" value="" class="form-control">
    </div>

    <div class="form-group span-full">
        <label>Address</label>
        <input type="text" name="" value="" class="form-control">
    </div>
</form>
```

For more flexible control of the design of the form, you can also use Bootstrap's Grid system to control the width of the form fields.

> **NOTE:** The Winter CMS Backend uses the Bootstrap v3 [Grid system](https://getbootstrap.com/docs/3.4/css/#gridhttps://getbootstrap.com/docs/3.4/css/#grid).

```html
<form class="form-elements">
    <div class="form-group col-xs-4">
        <label>First name</label>
        <input type="text" name="" value="" class="form-control">
    </div>

    <div class="form-group col-xs-4">
        <label>Last name</label>
        <input type="text" name="" value="" class="form-control">
    </div>

    <div class="form-group col-xs-4">
        <label>Address</label>
        <input type="text" name="" value="" class="form-control">
    </div>
</form>
```

```backend
<form class="form-elements">
    <div class="form-group col-xs-4">
        <label>First name</label>
        <input type="text" name="" value="" class="form-control">
    </div>

    <div class="form-group col-xs-4">
        <label>Last name</label>
        <input type="text" name="" value="" class="form-control">
    </div>

    <div class="form-group col-xs-4">
        <label>Address</label>
        <input type="text" name="" value="" class="form-control">
    </div>
</form>
```

## Required fields

Adding the class `is-required` to the `form-group` container of a field visually indicates to the user that this field is required. Note that this does not "force" the field to be entered - instead, you must enforce this via client-side or server-side validation.

```html
<form>
    <div class="form-group is-required">
        <label for="name">Your name</label>
        <input type="text" name="name" value="" class="form-control">
    </div>
</form>
```

```backend
<form>
    <div class="form-group is-required">
        <label for="name">Your name</label>
        <input type="text" name="name" value="" class="form-control">
    </div>
</form>
```

## Disabled and read-only fields

Adding a `disabled` or `readonly` attribute to the input (`form-contol`) will make the field either disabled or read-only. A disabled field cannot be interacted with at all, and will not be submitted with the form. A read-only field will be submitted with the form, but cannot be modified.

```html
<form class="form-elements">
    <div class="form-group span-left">
        <label>First name</label>
        <input type="text" name="" value="John" class="form-control" disabled>
    </div>

    <div class="form-group span-right">
        <label>Last name</label>
        <input type="text" name="" value="Person" class="form-control" readonly>
    </div>
</form>
```

```backend
<form class="form-elements">
    <div class="form-group span-left">
        <label>First name</label>
        <input type="text" name="" value="John" class="form-control" disabled>
    </div>

    <div class="form-group span-right">
        <label>Last name</label>
        <input type="text" name="" value="Person" class="form-control" readonly>
    </div>
</form>
```

## Complete example

The following is a complete example of a form, including all field types.

```html
<!-- Form Elements -->
<form class="form-elements" role="form">

    <!-- Text Input (Left) -->
    <div class="form-group text-field span-left is-required">
        <label>Input Left</label>
        <input type="text" name="" value="" class="form-control" />
            <p class="help-block">Example below help text here.</p>
    </div>

    <!-- Text Input (Right) -->
    <div class="form-group text-field span-right is-required">
        <label>Input Right</label>
        <input type="text" name="" value="" class="form-control" />
        <p class="help-block">Example below help text here.</p>
    </div>

    <!-- Text Input (Full) -->
    <div class="form-group text-field span-full is-required">
        <label>Input Full</label>
        <p class="help-block before-field">Example above help text here.</p>
        <input type="text" name="" value="" class="form-control" />
    </div>

    <!-- Drop down -->
    <div class="form-group dropdown-field span-left">
        <label>Drop Down</label>
        <select class="form-control custom-select">
            <option selected="selected" value="2">Approved</option>
            <option value="3">Deleted</option>
            <option value="1">New</option>
        </select>
    </div>

    <!-- Grouped Drop down -->
    <div class="form-group dropdown-field span-right">
        <label>Grouped Drop Down</label>
        <select class="form-control custom-select">
            <optgroup label="NFC EAST">
                <option>Dallas Cowboys</option>
                <option>New York Giants</option>
                <option>Philadelphia Eagles</option>
                <option>Washington Redskins</option>
            </optgroup><optgroup>
            </optgroup><optgroup label="NFC NORTH">
                <option>Chicago Bears</option>
                <option>Detroit Lions</option>
                <option>Green Bay Packers</option>
                <option>Minnesota Vikings</option>
            </optgroup>
            <optgroup label="NFC SOUTH">
                <option>Atlanta Falcons</option>
                <option>Carolina Panthers</option>
                <option>New Orleans Saints</option>
                <option>Tampa Bay Buccaneers</option>
            </optgroup>
            <optgroup label="NFC WEST">
                <option>Arizona Cardinals</option>
                <option>St. Louis Rams</option>
                <option>San Francisco 49ers</option>
                <option>Seattle Seahawks</option>
            </optgroup>
            <optgroup label="AFC EAST">
                <option>Buffalo Bills</option>
                <option>Miami Dolphins</option>
                <option>New England Patriots</option>
                <option>New York Jets</option>
            </optgroup>
            <optgroup label="AFC NORTH">
                <option>Baltimore Ravens</option>
                <option>Cincinnati Bengals</option>
                <option>Cleveland Browns</option>
                <option>Pittsburgh Steelers</option>
            </optgroup>
            <optgroup label="AFC SOUTH">
                <option>Houston Texans</option>
                <option>Indianapolis Colts</option>
                <option>Jacksonville Jaguars</option>
                <option>Tennessee Titans</option>
            </optgroup>
            <optgroup label="AFC WEST">
                <option>Denver Broncos</option>
                <option>Kansas City Chiefs</option>
                <option>Oakland Raiders</option>
                <option>San Diego Chargers</option>
            </optgroup>
        </select>
    </div>

    <!-- Checkbox -->
    <div class="form-group checkbox-field span-left is-required">
        <div class="checkbox custom-checkbox">
            <input name="checkbox" value="1" type="checkbox" id="checkbox_1">
            <label for="checkbox_1">Enable Googie Berry Power-up</label>
            <p class="help-block">Use this checkbox to enable the Googie Berry power-up specifically for this page. You can configure the Googie Berry power-up on the System Settings and Dashboard page.</p>
        </div>
    </div>

    <!-- Switcher -->
    <div class="form-group switch-field span-right">
        <div class="field-switch">
            <label>Would you like fries with that?</label>
            <p class="help-block">Use this checkbox to enable the Googie Berry power-up specifically for this page. You can configure the Googie Berry power-up on the System Settings and Dashboard page.</p>
        </div>
        <label class="custom-switch">
            <input type="checkbox" />
            <span><span>On</span><span>Off</span></span>
            <a class="slide-button"></a>
        </label>
    </div>

    <!-- Radio List -->
    <div class="form-group radio-field span-left is-required">
        <label>Radio List</label>
        <p class="help-block before-field">Where should you propose to your beautiful girl?</p>

        <div class="radio custom-radio">
            <input name="radio" value="1" type="radio" id="radio_1">
            <label for="radio_1">Paris</label>
            <p class="help-block">Do not send new comment notifications.</p>
        </div>
        <div class="radio custom-radio">
            <input checked="checked" name="radio" value="2" type="radio" id="radio_2">
            <label for="radio_2">Dubai</label>
            <p class="help-block">Send new comment notifications only to post author.</p>
        </div>
        <div class="radio custom-radio">
            <input name="radio" value="3" type="radio" id="radio_3">
            <label for="radio_3">New Zealand</label>
            <p class="help-block">Notify all users who have permissions to receive blog notifications.</p>
        </div>
    </div>

    <!-- Checkbox List -->
    <div class="form-group checkboxlist-field span-right is-required">
        <label>Checkbox List</label>
        <p class="help-block before-field">What cars would you like in your garage?</p>

        <div class="checkbox custom-checkbox">
            <input id="checkbox-example1" name="checkbox" value="1" type="checkbox">
            <label class="choice" for="checkbox-example1"> Dodge Viper</label>
            <p class="help-block">Do not send new comment notifications.</p>
        </div>
        <div class="checkbox custom-checkbox">
            <input checked="checked" id="checkbox-example2" name="checkbox" value="2" type="checkbox">
            <label class="choice" for="checkbox-example2"> GM Corvette</label>
            <p class="help-block">Send new comment notifications only to post author.</p>
        </div>
        <div class="checkbox custom-checkbox">
            <input id="checkbox-example3" name="checkbox" value="3" type="checkbox">
            <label class="choice" for="checkbox-example3"> Porsche Boxter</label>
            <p class="help-block">Notify all users who have permissions to receive blog notifications.</p>
        </div>
    </div>

</form>
```

```backend
<!-- Form Elements -->
<form class="form-elements" role="form">

    <!-- Text Input (Left) -->
    <div class="form-group text-field span-left is-required">
        <label>Input Left</label>
        <input type="text" name="" value="" class="form-control" />
            <p class="help-block">Example below help text here.</p>
    </div>

    <!-- Text Input (Right) -->
    <div class="form-group text-field span-right is-required">
        <label>Input Right</label>
        <input type="text" name="" value="" class="form-control" />
        <p class="help-block">Example below help text here.</p>
    </div>

    <!-- Text Input (Full) -->
    <div class="form-group text-field span-full is-required">
        <label>Input Full</label>
        <p class="help-block before-field">Example above help text here.</p>
        <input type="text" name="" value="" class="form-control" />
    </div>

    <!-- Drop down -->
    <div class="form-group dropdown-field span-left">
        <label>Drop Down</label>
        <select class="form-control custom-select">
            <option selected="selected" value="2">Approved</option>
            <option value="3">Deleted</option>
            <option value="1">New</option>
        </select>
    </div>

    <!-- Grouped Drop down -->
    <div class="form-group dropdown-field span-right">
        <label>Grouped Drop Down</label>
        <select class="form-control custom-select">
            <optgroup label="NFC EAST">
                <option>Dallas Cowboys</option>
                <option>New York Giants</option>
                <option>Philadelphia Eagles</option>
                <option>Washington Redskins</option>
            </optgroup><optgroup>
            </optgroup><optgroup label="NFC NORTH">
                <option>Chicago Bears</option>
                <option>Detroit Lions</option>
                <option>Green Bay Packers</option>
                <option>Minnesota Vikings</option>
            </optgroup>
            <optgroup label="NFC SOUTH">
                <option>Atlanta Falcons</option>
                <option>Carolina Panthers</option>
                <option>New Orleans Saints</option>
                <option>Tampa Bay Buccaneers</option>
            </optgroup>
            <optgroup label="NFC WEST">
                <option>Arizona Cardinals</option>
                <option>St. Louis Rams</option>
                <option>San Francisco 49ers</option>
                <option>Seattle Seahawks</option>
            </optgroup>
            <optgroup label="AFC EAST">
                <option>Buffalo Bills</option>
                <option>Miami Dolphins</option>
                <option>New England Patriots</option>
                <option>New York Jets</option>
            </optgroup>
            <optgroup label="AFC NORTH">
                <option>Baltimore Ravens</option>
                <option>Cincinnati Bengals</option>
                <option>Cleveland Browns</option>
                <option>Pittsburgh Steelers</option>
            </optgroup>
            <optgroup label="AFC SOUTH">
                <option>Houston Texans</option>
                <option>Indianapolis Colts</option>
                <option>Jacksonville Jaguars</option>
                <option>Tennessee Titans</option>
            </optgroup>
            <optgroup label="AFC WEST">
                <option>Denver Broncos</option>
                <option>Kansas City Chiefs</option>
                <option>Oakland Raiders</option>
                <option>San Diego Chargers</option>
            </optgroup>
        </select>
    </div>

    <!-- Checkbox -->
    <div class="form-group checkbox-field span-left is-required">
        <div class="checkbox custom-checkbox">
            <input name="checkbox" value="1" type="checkbox" id="checkbox_1">
            <label for="checkbox_1">Enable Googie Berry Power-up</label>
            <p class="help-block">Use this checkbox to enable the Googie Berry power-up specifically for this page. You can configure the Googie Berry power-up on the System Settings and Dashboard page.</p>
        </div>
    </div>

    <!-- Switcher -->
    <div class="form-group switch-field span-right">
        <div class="field-switch">
            <label>Would you like fries with that?</label>
            <p class="help-block">Use this checkbox to enable the Googie Berry power-up specifically for this page. You can configure the Googie Berry power-up on the System Settings and Dashboard page.</p>
        </div>
        <label class="custom-switch">
            <input type="checkbox" />
            <span><span>On</span><span>Off</span></span>
            <a class="slide-button"></a>
        </label>
    </div>

    <!-- Radio List -->
    <div class="form-group radio-field span-left is-required">
        <label>Radio List</label>
        <p class="help-block before-field">Where should you propose to your beautiful girl?</p>

        <div class="radio custom-radio">
            <input name="radio" value="1" type="radio" id="radio_1">
            <label for="radio_1">Paris</label>
            <p class="help-block">Do not send new comment notifications.</p>
        </div>
        <div class="radio custom-radio">
            <input checked="checked" name="radio" value="2" type="radio" id="radio_2">
            <label for="radio_2">Dubai</label>
            <p class="help-block">Send new comment notifications only to post author.</p>
        </div>
        <div class="radio custom-radio">
            <input name="radio" value="3" type="radio" id="radio_3">
            <label for="radio_3">New Zealand</label>
            <p class="help-block">Notify all users who have permissions to receive blog notifications.</p>
        </div>
    </div>

    <!-- Checkbox List -->
    <div class="form-group checkboxlist-field span-right is-required">
        <label>Checkbox List</label>
        <p class="help-block before-field">What cars would you like in your garage?</p>

        <div class="checkbox custom-checkbox">
            <input id="checkbox-example1" name="checkbox" value="1" type="checkbox">
            <label class="choice" for="checkbox-example1"> Dodge Viper</label>
            <p class="help-block">Do not send new comment notifications.</p>
        </div>
        <div class="checkbox custom-checkbox">
            <input checked="checked" id="checkbox-example2" name="checkbox" value="2" type="checkbox">
            <label class="choice" for="checkbox-example2"> GM Corvette</label>
            <p class="help-block">Send new comment notifications only to post author.</p>
        </div>
        <div class="checkbox custom-checkbox">
            <input id="checkbox-example3" name="checkbox" value="3" type="checkbox">
            <label class="choice" for="checkbox-example3"> Porsche Boxter</label>
            <p class="help-block">Notify all users who have permissions to receive blog notifications.</p>
        </div>
    </div>

</form>
```
