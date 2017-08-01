FlexForms Class:  'support/flex_forms.php'
==========================================

FlexForms is a powerful HTML forms generator/builder class to output HTML forms from PHP using a natural PHP arrays approach.

Example usage:

```php
<?php
	require_once "support/str_basics.php";
	require_once "support/flex_forms.php";

	// FlexForms Extras adds powerful date and multiselect functionality.
//	require_once "support/flex_forms_extras.php";

	// Standard global application initialization.
	Str::ProcessAllInput();

	$ff = new FlexForms();

	// Try random.org - https://www.random.org/integers/?num=100&min=0&max=255&col=10&base=16&format=plain&rnd=new
	$ff->SetSecretKey("[some random data goes here - e.g. internal secret + user secret]");
//	$ff->SetTokenExtraInfo("[extra user-specific information could go here - not intended for secret information]");
	$ff->CheckSecurityToken("action");

	if (isset($_REQUEST["action"]) && $_REQUEST["action"] == "myform")
	{
		// Check form submissions.
		$errors = array();
		if (isset($_REQUEST[$ff->GetHashedFieldName("name")]))
		{
			$defaults = array(
				"name" => "",
				"email" => "",
				"categories" => array(),
				"comments" => ""
			);

			$fields = $ff->GetHashedFieldValues($defaults);

			if ($fields["name"] == "")  $errors["name"] = "Please fill in this field.";
			if ($fields["email"] == "")  $errors["email"] = "Please fill in this field.";
			if (!count($fields["categories"]))  $errors["categories"] = "Please select at least one category.";
			if ($fields["comment"] == "")  $errors["comment"] = "Please fill in this field.";

			if (!count($errors))
			{
				// Do something here (e.g. send an e-mail or store the information in a database).

				header("Location: " . $ff->GetFullRequestURLBase() . "?" . $ff->GetEncodedSignedMessage("success", "Your comment was submitted successfully."));
			}
		}

?>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<title>My form</title>
</head>
<body>
<?php

		// Output messages.
		if (count($errors))  $ff->OutputMessage("error", "Please correct the errors below to continue.");
		else  $ff->OutputSignedMessage();

		// Prepare the form content options array.
		$contentopts = array(
			"hashnames" => true,

			// FlexForms automatically generates correct values for required hidden fields (i.e. "action").
//			"nonce" => "action",
//			"hidden" => array(
//				"action" => "myform"
//			),
			"fields" => array(
				array(
					"title" => "* Name",
					"type" => "text",
					"name" => "name",
					"default" => ""
				),
				array(
					"title" => "* Email Address",
					"type" => "text",
					"name" => "email",
					"default" => ""
				),
				array(
					"title" => "* Categories",
					"type" => "select",
					"multiple" => true,
					"mode" => "tags",  // This mode is only available with FlexForms Extras.  If it isn't available and 'multiple' is true, the default is to display checkboxes.
					"name" => "categories",
					"options" => array("broken_link" => "Broken link", "compliment" => "Compliment", "complaint" => "Complaint"),
					"default" => array("compliment")
				),
				array(
					"title" => "* Comment",
					"height" => "300px",
					"type" => "textarea",
					"name" => "comment",
					"default" => ""
				)
			),
			"submit" => "Submit"
		);

		$ff->Generate($contentopts, $errors);
?>
</body>
</html>
<?php
	}
	else
	{
?>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<title>Main page</title>
</head>
<body>
<?php
		$ff->OutputSignedMessage();
?>
<p>
	<a href="index.php?action=myform&sec_t=<?=$ff->CreateSecurityToken("myform")?>">Go to the form</a>
</p>
</body>
</html>
<?php
	}
?>
```

FlexForms::GetState()
---------------------

Access:  public

Parameters:  None.

Returns:  The internal state array.

This function returns the current internal state of the class.  The information can be used to save the state for later use with SetState().

FlexForms::SetState($newstate)
------------------------------

Access:  public

Parameters:

* $newstate - An array of key-value pairs to merge into the main internal state.

Returns:  Nothing.

This function sets new state information.  Can be used to restore a previous state from a GetState() call or alter the state information to bypass default functionality.

The built-in states are:

* formnum - An integer containing the last form number output (Default is 0).
* formidbase - A string containing the form ID prefix (Default is "ff_form_").  When Generate() is called, the "formid" state is assigned a value of formidbase + formnum.
* responsive - A boolean that indicates whether ot not to enable responsive output for small screen support (Default is true).
* formtables - A boolean that indicates whether or not to allow output of HTML tables (Default is true).
* formwidths - A boolean that indicates whether or not to allow output of field widths (Default is true).
* autofocused - A boolean of false or a string that indicates what field (if any) should receive keyboard caret focus (Default is false).
* jqueryuiused - A boolean that indicates whether or not the jQuery UI is used/needed (Default is false).  FlexForms Extras brings several jQuery UI components to the party.
* jqueryuitheme - A string containing the jQuery UI theme to use if jQuery UI is used (Default is "smoothness").
* supporturl - A string containing the URL path to the "support" directory (Default is "support").
* customfieldtypes - An array containing key-value pairs of types that are completely customized by registered form handlers (Default is array()).
* ajax - A boolean that indicates whether or not to use AJAX mode (Default is false).  Do not modify this directly.  Call SetAjax() instead.
* action - A string containing the URL to use for a HTML form "action" attribute (Default is the result of self::GetRequestURLBase()).
* js - An array of Javascript dependencies to resolve and output (Default is array()).
* jsoutput - An array of Javascript dependencies already output (Default is array()).
* css - An array of CSS dependencies to resolve and output (Default is array()).
* cssoutput - An array of CSS dependencies already output (Default is array()).

FlexForms::SetAjax($enable)
---------------------------

Access:  public

Parameters:

* $enable - A boolean that indicates whether or not to enable AJAX mode.

Returns:  Nothing.

This function enables or disables AJAX mode for FlexForms.  AJAX mode is disabled by default.

FlexForms::AddJS($name, $info)
------------------------------

Access:  public

Parameters:

* $name - A string containing the internal name for the Javascript resource.
* $info - An array containing valid Javascript file details, including dependency information.

Returns:  Nothing.

This function can be used to register extra Javascript that should be output when Generate() is called.

FlexForms::SetJSOutput($name)
-----------------------------

Access:  public

Parameters:

* $name - A string containing the internal name for the Javascript resource.

Returns:  Nothing.

This function can be used to ignore Javascript that is output before the function is called (e.g. jQuery).

FlexForms::AddCSS($name, $info)
-------------------------------

Access:  public

Parameters:

* $name - A string containing the internal name for the CSS resource.
* $info - An array containing valid CSS file details, including dependency information.

Returns:  Nothing.

This function can be used to register extra CSS that should be output when Generate() is called.

FlexForms::SetCSSOutput($name)
-----------------------------

Access:  public

Parameters:

* $name - A string containing the internal name for the CSS resource.

Returns:  Nothing.

This function can be used to ignore CSS that is output before the function is called (e.g. FlexForms default CSS).

FlexForms::SetSecretKey($secretkey)
-----------------------------------

Access:  public

Parameters:

* $secretkey - A string containing random, known data.

Returns:  Nothing.

This function sets the internal secret key for specifying tokens and random field names.  FlexForms will not function without specifying a secret key.  The data in the secret key should never be sent to the user's web browser.

For a static key, try [random.org](https://www.random.org/integers/?num=100&min=0&max=255&col=10&base=16&format=plain&rnd=new).

For combining a static key with a per-user or per-session key, use [CSPRNG](https://github.com/cubiclesoft/php-csprng/).

FlexForms::SetTokenExtraInfo($extrainfo)
----------------------------------------

Access:  public

Parameters:

* $extrainfo - A string containing extra information to include in the data portion while generating security tokens.

Returns:  Nothing.

This function takes in extra information to feed into CreateSecurityToken().  It doesn't have to be random data but the data should not be considered secure.

FlexForms::CreateSecurityToken($action, $extra = "")
----------------------------------------------------

Access:  public

Parameters:

* $action - A string containing the future target action request variable.
* $extra - A string or an array containing extra information or an empty string (Default is "").

Returns:  A string containing a security token.

This function is used to generate security tokens to defend against XSS/CSRF attacks.  Security tokens are used later with CheckSecurityToken(), which verifies that the security token sent by the web browser matches before continuing.

FlexForms::IsSecExtraOpt($opt)
------------------------------

Access:  public static

Parameters:

* $opt - A string containing an option to verify as being used to generate the security token.

Returns:  A boolean indicating whether or not the option is valid.

This static function verifies that "sec_extra" exists and contains the specified option.  This is an advanced feature that is rarely used.

FlexForms::CheckSecurityToken($action)
--------------------------------------

Access:  public

Parameters:

* $action - A string containing the request variable to check the security token against.

Returns:  Nothing.

This function verifies the required "sec_t" and optional "sec_extra" request variables against the internally calculated security token.  If the action exists but the security token is missing or does not precisely match, the application will immediately exit with an "Invalid security token" message.

This function defends against XSS/CSRF attacks.  Never write advanced form code without proper XSS/CSRF defenses.

If the specified action exists and passes the security check, the specified action is assigned to the internal automatic nonce tracker.  This is later used during Generate() for automatic form looping and reduce error-prone issues from copying and pasting code.

FlexForms::OutputFormCSS($delaycss = false)
-------------------------------------------

Access:  public

Parameters:

* $delaycss - A boolean indicating whether or not to delay outputting the default FlexForms CSS file (Default is false).

Returns:  Nothing.

This function either outputs the default FlexForms CSS file immediately or delays output until Finalize() is called (the last form output).  If alternate CSS is being used, call `$ff->SetCSSOutput("formcss")` so that this function won't output the FlexForms CSS file at all.

FlexForms::OutputMessage($type, $message)
-----------------------------------------

Access:  public

Parameters:

* $type - A string containing one of "success", "warning", "error", or "info".
* $message - A string containing the message to output.

Returns:  Nothing.

This function displays a message to the user with specific styling.  Useful for displaying a generic error message near the top of the page if there is a problem with the user's form submission.  Note that the message is not HTML encoded, so be careful to call htmlspecialchars() on any user-submitted data passed to this function.

FlexForms::GetEncodedSignedMessage($type, $message, $prefix = "")
-----------------------------------------------------------------

Access:  public

Parameters:

* $type - A string containing one of "success", "warning", "error", or "info".
* $message - A string containing the message to sign.
* $prefix - A string containing the prefix string to use (Default is "").

Returns:  A string correctly encoded for use in a URL.

This function signs the type and message and returns a string suitable for use with a URL.  The information can be used later with OutputSignedMessage() to display the message to the user.  This function prevents URL-based Javascript injection.

FlexForms::OutputSignedMessage($prefix = "")
--------------------------------------------

Access:  public

Parameters:

* $prefix - A string containing the prefix string used to generate a signed message (Default is "").

Returns:  Nothing.

This function verifies that a signed message exists and that the signature is valid before displaying the message to the user.  This function prevents URL-based Javascript injection.

FlexForms::OutputJQuery($delayjs = false)
-----------------------------------------

Access:  public

* $delaycss - A boolean indicating whether or not to delay outputting the jQuery library (Default is false).

Returns:  Nothing.

This function either outputs the jQuery library immediately or delays output until Finalize() is called (the last form output).  If jQuery is already output, call `$ff->SetJSOutput("jquery")` so that this function won't also attempt to output jQuery.

FlexForms::OutputJQueryUI($delayjs = false)
-----------------------------------------

Access:  public

* $delaycss - A boolean indicating whether or not to delay outputting the jQuery UI library (Default is false).

Returns:  Nothing.

This function either outputs the jQuery UI library immediately or delays output until Finalize() is called (the last form output).  If jQuery UI is already output, call `$ff->SetJSOutput("jqueryui")` so that this function won't also attempt to output jQuery UI.

FlexForms::NormalizeFiles($key)
-------------------------------

Access:  public static

Parameters:

* $key - A string containing the file input parameter to look for.

Returns:  An array of standard arrays of information about associated files.

This function determines if the request file input parameter exists and returns a validated multidimensional array of information.  This function handles common functionality for the PHP `$_FILES` array (e.g. calling `is_uploaded_file()`) and reduces code complexity especially when processing multidimensional file submissions.

FlexForms::GetValue($key, $default)
-----------------------------------

Access:  public static

Parameters:

* $key - A string containing the input parameter to look for.
* $default - The default value to use if the request input parameter is not found.

Returns:  The value of the request input parameter or the default value.

This function determines if the request input parameter exists and returns it.  If it doesn't exist, the default value is returned.  The direct use of this function is no longer considered to be best practice since FlexForms supports a "default" key-value pair for each field.  Less code equates to fewer problems.

FlexForms::GetSelectValues($data)
---------------------------------

Access:  public static

Parameters:

* $data - An array of values.

Returns:  The array of values made into keys mapped to true.

This function takes in an input array of values and prepares them for use as values for a "select" key in a field.  The direct use of this function is no longer considered to be best practice since FlexForms supports a "default" key-value pair for each field.  Less code equates to fewer problems.

FlexForms::ProcessInfoDefaults($info, $defaults)
------------------------------------------------

Access:  public static

Parameters:

* $info - An array to merge defaults into if keys are missing.
* $defaults - An array containing default key-value pairs.

Returns:  A merged array of key-value pairs.

This function merges missing defaults into an array of information.  This function is useful for loading serialized data from a database into a consistent format.  The use of this type of approach reduces potential code breakage when new fields are added.

FlexForms::SetNestedPathValue(&$data, $pathparts, $val)
-------------------------------------------------------

Access:  public static

Parameters:

* $data - A multidimensional array of information.
* $pathparts - An array representing the path in $data to walk down.
* $val - The new value at the location in $data specified by $pathparts.

Returns:  Nothing.

This function updates the input data array with the specified value.  The provided functionality can be useful for manipulating complex array structures.  The FlexForms charts module allows for custom options to be passed to C3.js via this function.

FlexForms::GetIDDiff($origids, $newids)
---------------------------------------

Access:  public static

Parameters:

* $origids - An array containing key-value pairs of original ID mapped to information.
* $newids - An array containing key-value pairs of new ID mapped to information.

Returns:  An array containing the IDs added and removed.

This function can be used to reduce the number of operations that need to be performed to make necessary changes to a database.  For example, a table that links two tables together via IDs and the user is allowed to add and remove links.  The naive approach is remove all links and then add the ones that the user wants.  This function could be used to minimize the number of DELETE FROM and INSERT INTO statements that are executed to just those that are needed.

FlexForms::GetHashedFieldName($name)
------------------------------------

Access:  public

Parameters:

* $name - A string containing the name of a request parameter.

Returns:  The security HMAC version of the field name.

This function returns what the expected field name should be using the current secret key.  It can be used to determine if the field exists and what it's value actually is.

Example usage:

```php
<?php
	$email = $ff->GetHashedFieldName("email");
	if (isset($_REQUEST[$email]))
	{
		// Do something with the e-mail address.
	}
?>
```

FlexForms::GetHashedFieldValues($nameswithdefaults)
---------------------------------------------------

Access:  public

Parameters:

* $nameswithdefaults - An array containing key-value pairs to map field names to their defaults if they do not exist.

Returns:  An array containing field name mappings to either values or defaults.

This function bulk processes input fields were named using a HMAC into an array that guarantees the input keys exist in the output array.  This is the easiest and most performance-friendly way to avoid calling GetHashedFieldName() repeatedly to access submitted form data when field names have been hashed.

FlexForms::Generate($options, $errors = array(), $lastform = true)
------------------------------------------------------------------

Access:  public

Parameters:

* $options - An array containing the necessary content to generate the HTML form.
* $errors - An array of key-value pairs that maps error messages to specific fields by name (Default is array()).
* $lastform - A boolean that specifies whether or not this is the last form being output by FlexForms for the current request (Default is true).

Returns:  Nothing.

This function takes in an input options array and an optional error mapping array and generates a HTML form, including all the necessary pieces to make the form function properly on the web browser side of things.

Finalize() is not called unless $lastform is true.  This can be useful for displaying multiple forms on one page using FlexForms.  However, doing so is not recommended since it could be confusing to the user.

The $options array accepts these options:

* hashnames - A boolean that indicates whether or not to hash the names using HMAC that are used for fields and the submit buttons.  Submitted field names by the browser can later be accessed via GetHashedFieldName() and GetHashedFieldValues().
* nonce - A string containing the name of the action field.  If CheckSecurityToken() is used properly, this option is automatically handled when form looping.
* hidden - An array containing key-value pairs of hidden values to pass back to the server.  When "nonce" is specified, these fields are are automatically digitally signed with an HMAC.
* fields - An array containing the fields to display on the form.
* submit - A string containing the value or an array of key-values containing the submit buttons to display.
* submitname - A string containing the name of the submit button.
* useform - A boolean indicating whether or not to output form tags and hidden fields even if a submit button won't be included in the output.  Use of this option is rare.
* formmode - A string containing one of "post" or "get" (Default is "post").

The fields array is very flexible and options vary depending on the field.  A field can be a string or an array.  Built-in string types:

* split - Outputs a horizontal rule (hr) tag.  This is different from the array field "split" option.
* startrow - Starts a new table row.  Useful for compacting many elements together.
* endrow - Ends the current table.
* html: - A special prefix string that indicates that the rest of the string should be output as-is.

Each array field may have the following options (only "type" is required for all arrays):

* use - A boolean that indicates whether or not to even output the field.  Useful for permissions-based field display.
* split - A boolean that indicates whether or not the field's wrapping div should receive a class of "firstitem".
* title - A string containing the field's title.  HTML encoded for safety.
* htmltitle - A string containing the field's title.  Not HTML encoded.  Not used if "title" is specified.
* width - A string containing a valid CSS width.
* height - A string containing a valid CSS height.
* type - A string containing the type of field to output.  FlexForms itself defines and implements "static", "text", "textarea", "password", "checkbox", "select", "table", "file", and "custom" types.
* name - A string containing the name to use for the field.  This option applies to the "text", "textarea", "password", "checkbox", "select", and "file" types.  If hashed names are used, the field name will be hashed accordingly.
* value - A string containing the value of the field.  GetValue() can be used to select either user-submitting information or a default value.  This option applies to the "static", "text", "textarea", "password", "checkbox", and "custom" types.  It is recommended to use "default" instead, which will accomplish the same thing with less code.
* default - The default value to use for the field.  This option applies to the "static", "text", "textarea", "password", "checkbox", "select", and "custom" types.  For the "checkbox" type, this decides the default status of the checkbox.
* desc - A string containing the field's helpful description text.  HTML encoded for safety.
* htmldesc - A string containing the field's helpful description text.  Not HTML encoded.  Not used if "desc" is specified.
* focus - A boolean of true indicates that the HTML 'autofocus' attribute should be applied to the element if it hasn't been applied to some other element.

Type-specific options for array fields:

* check (checkbox) - A boolean that indicates whether or not the checkbox is checked.
* display (checkbox) - A string to display next to the checkbox inside of a label tag.
* options (select) - An array of key-value pairs.  If the values are arrays, the keys are treated as optgroup labels and the values are the options to display for the optgroup.
* multiple (select) - A boolean that indicates whether or not this is a multiple selection element.  Note that FlexForms does not readily display the built-in multiselect element because it is extremely unusable.
* mode (select) - A string containing one of "select", "radio", or "checkbox" (multiple only).  FlexForms form handlers may change an input mode to the special string "formhandler".
* cols (table) - An array of column headers.  Not HTML encoded.
* rows (table) - An array of arrays where each subarray represents a row.  Not HTML encoded.  Note that collecting thousands of rows of data may cause PHP to run out of memory.  Use the "callback" option to output rows as they are processed.
* nowrap (table) - An array of columns to apply a 'nowrap' class to.  It is up to CSS to actually apply the relevant style.
* callback (table) - A valid callback function for a callback that returns the next set of rows to process.  The callback function must accept no parameters - callback().
* multiple (file) - A boolean that indicates whether or not this is a multiple file selection element.  Browser support varies.

FlexForms::FFTranslate($format, ...)
------------------------------------

Access:  _internal_ static

Parameters:

* $format - A string containing valid sprintf() format specifiers.

Returns:  A string containing a translation.

This mostly internal static function takes input strings and translates them from English to some other language if CS_TRANSLATE_FUNC is defined to be a valid PHP function name.  FlexForms extensions may use this function for translation services.

FlexForms::JSSafe($data)
------------------------

Access:  public static

Parameters:

* $data - A string.

Returns: A string containing data that is safe for use in Javascript.

This static function creates an error-free Javascript string.  Note that only single-quotes are escaped.

FlexForms::IsSSLRequest()
-------------------------

Access:  public static

Parameters:  None.

Returns:  A boolean of true if the browser is loading the page via SSL, false otherwise.

This static function attempts to detect a SSL connection.  Not all web servers accurately provide the status of SSL to scripting languages.

FlexForms::GetRequestHost($protocol = "")
-----------------------------------------

Access:  public static

Parameters:

* $protocol - A string containing one of "", "http", or "https" (Default is "").

Returns:  A string containing the host in URL format.

This static function retrieves the host in URL format and looks like `http[s]://www.something.com[:port]` based on the current page request.  The result of this function is cached.  The $protocol parameter defaults to whatever type the connection is detected with BB_IsSSLRequest() but can be overridden by specifying "http" or "https".

FlexForms::GetRequestURLBase()
------------------------------

Access:  public static

Parameters:  None.

Returns:  A string containing the path part of the request URL (excludes query string).

This static function retrieves the path of request URL.  The $_REQUEST["REQUEST_URI"] variable is parsed and the protocol, host, and query string parts are removed if they exist.  This function is used to calculate the destination for generated forms.

FlexForms::GetFullRequestURLBase($protocol = "")
------------------------------------------------

Access:  public static

Parameters:

* $protocol - A string containing one of "", "http", or "https" (Default is "").

Returns: A string containing the full request URL.

This function combines GetRequestHost() and GetRequestURLBase() to obtain the full request URL.

FlexForms::RegisterFormHandler($mode, $callback)
------------------------------------------------

Access:  public static

Parameters:

* $mode - A string containing one of "init", "field_string", "field_type", "table_row", "cleanup", or "finalize".
* $callback - A valid callback function for the specified mode.  The callback function must accept the correct number and type of inputs.

Returns:  Nothing.

This static function is the basis of adding new functionality to the FlexForms class.  Callbacks are executed in a specific sequence for managing field modifications in an efficient manner.

Example usage:

[FlexForms Extras](https://github.com/cubiclesoft/admin-pack-with-extras/blob/master/support/flex_forms_extras.php)

FlexForms::InitFormVars(&$options)
----------------------------------

Access:  protected

Parameters:

* $options - An array containing content used to generate the HTML form.

Returns:  Nothing.

This internal function initializes FlexForms and any registered "init" form handlers for generating a new form.  The $options array and local state may be modified.

FlexForms::AlterField($num, &$field, $id)
-----------------------------------------

Access:  protected

Parameters:

* $num - An integer representing the current field number from the original options array.
* $field - An array containing the current field information.
* $id - A string containing a unique ID compatible with HTML IDs.

Returns:  Nothing.

This internal function is called when a field with a "type" is to be displayed.  Any registered "field_type" form handlers are run.  The $field and local state may be modified by form handlers.

FlexForms::ProcessField($num, &$field, $id)
-------------------------------------------

Access:  protected

Parameters:

* $num - An integer representing the current field number from the original options array.
* $field - A string or an array containing the current field information.
* $id - A string containing a unique ID compatible with HTML IDs.

Returns:  Nothing.

This internal function processes the input field and outputs HTML as part of the processing phase of Generate() through each field.  Registered field-specific form handlers run.  String fields are handled differently from array fields.  The $field and local state may be modified by form handlers.

FlexForms::CleanupFields()
--------------------------

Access:  protected

Parameters:  None.

Returns:  Nothing.

This internal function processes state and cleans up remaining open HTML tags.  Any registered "cleanup" form handlers are run.  The local state may be modified by form handlers.

FlexForms::ProcessSubmit(&$options)
-----------------------------------

Access:  protected

Parameters:

* $options - An array containing content used to generate the HTML form.

Returns:  Nothing.

This internal function processes the form options and generates one or more submit buttons.

FlexForms::Finalize()
---------------------

Access:  protected

Parameters:  None.

Returns:  Nothing.

This function finalizes the output for all forms emitted.  Collected CSS and Javascript content is written out in dependency order.  Any registered "finalize" form handlers are run.  The local state may be modified by form handlers.
