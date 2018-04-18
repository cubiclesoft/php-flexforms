Admin Pack Page Basics:  'support/page_basics.php'
==================================================

[Admin Pack](https://github.com/cubiclesoft/admin-pack) page basics is a set of global functions and variables that efficiently perform common tasks for Admin Pack-based web applications.  This documentation resides within [FlexForms](https://github.com/cubiclesoft/php-flexforms) since most of the functionality originally found in Admin Pack has been migrated to FlexForms and most functions are simply lightweight wrappers around FlexForms.

BB_FlexForms::CreateSecurityToken($action, $extra = "")
-------------------------------------------------------

Access:  public

Parameters:

* $action - A string containing an action.
* $extra - A string containin a list of extra information to include in the security token (Default is "").

Returns:  A string containing a security token.

This internal function is used by `BB_CreateSecurityToken()` and FlexForms to generate security tokens.  The global variables `$bb_randpage` and `$bb_usertoken` are used to construct the FlexForms secret key.

$bb_flexforms
-------------

Access:  global

This global variable is an instance of the BB_FlexForms class.  The BB_FlexForms class extends the FlexForms class to handle security token generation.  This was done for legacy reasons.

BB_JSSafe($data)
----------------

Access:  global

Parameters:

* $data - A string.

Returns:  A string containing data that is safe for use in Javascript.

This global function creates an error-free Javascript string.  Note that only single-quotes are escaped.

This function is a wrapper for `FlexForms::JSSafe()`.

BB_IsSSLRequest()
-----------------

Access:  global

Parameters:  None.

Returns:  A boolean of true if the browser is loading the page via SSL, false otherwise.

This global function attempts to detect a SSL connection.  Not all web servers accurately provide the status of SSL to scripting languages.

This function is a wrapper for `FlexForms::IsSSLRequest()`.

BB_GetRequestHost($protocol = "")
---------------------------------

Access:  global

Parameters:

* $protocol - A string containing one of "", "http", or "https" (Default is "").

Returns:  A string containing the host in URL format.

This global function retrieves the host in URL format and looks like `http[s]://www.something.com[:port]` based on the current page request.  The result of this function is cached.  The `$protocol` parameter defaults to whatever type the connection is detected with IsSSLRequest() but can be overridden by specifying "http" or "https".

This function is a wrapper for `FlexForms::GetRequestHost()`.

BB_GetRequestURLBase()
----------------------

Access:  global

Parameters:  None.

Returns:  A string containing the path part of the request URL (excludes query string).

This global function retrieves the path of request URL.  The $_SERVER["REQUEST_URI"] variable is parsed and the protocol, host, and query string parts are removed if they exist.  This function is used to calculate the destination for generated forms.

This function is a wrapper for `FlexForms::GetRequestURLBase()`.

BB_GetFullRequestURLBase($protocol = "")
----------------------------------------

Access:  global

Parameters:

* $protocol - A string containing one of "", "http", or "https" (Default is "").

Returns:  A string containing the full request URL.

This global function combines `GetRequestHost()` and `GetRequestURLBase()` to obtain the full request URL.

This function is a wrapper for `FlexForms::GetFullRequestURLBase()`.

BB_Translate($format, ...)
--------------------------

Access:  global

Parameters:

* $format - A string containing valid sprintf() format specifiers.

Returns:  A string containing a translation.

This global function provides multilingual translation of an input formatting string into a single output string based on the information in `$bb_langmap`, `$bb_admin_lang`, and `$bb_admin_def_lang`.

BB_PostTranslate($str)
----------------------

Access:  global

Parameters:

* $str - A string to run partial or complete translations of.

Returns:  A translated version of the string.

This global function runs specialized partial or complete translations of the input string based on the information in `$bb_langmap`, `$bb_admin_lang`, and `$bb_admin_def_lang`.

BB_FormatTimestamp($format, $ts)
--------------------------------

Access:  global

Parameters:

* $format - A string in PHP [date](http://php.net/manual/en/function.date.php) format.
* $ts - An integer containing a valid timestamp.

Returns:  A translated string containing the timestamp in a human-readable format.

This function runs the output of the PHP `date()` function through `BB_Translate()` and `BB_PostTranslate()` for display.

BB_SetLanguage($path, $lang)
----------------------------

Access:  global

Parameters:

* $path - A string containing a valid path to the language packs.
* $lang - A string containing the name of the language pack to load and set.

Returns:  A standard array of information.

This global function loads in the specified language pack (if not already loaded) and sets the `$bb_admin_lang` global variable to the language.

BB_InitLangmap($path, $default = "")
------------------------------------

Access:  global

Parameters:

* $path - A string containing a valid path to the language packs.
* $default - A string containing the name of the default language pack to load and set.

Returns:  Nothing.

Initializes `$bb_langmap`, `$bb_admin_lang`, and `$bb_admin_def_lang` based on the browser's preferences and available language packs.  Failures are ignored.

SetCookieFixDomain($name, $value = "", $expires = 0, $path = "", $domain = "", $secure = false, $httponly = false)
------------------------------------------------------------------------------------------------------------------

Access:  global

Parameters:

* $name - A string containing the name of the cookie to set.
* $value - A string containing the value of the cookie to set (Default is "").
* $expires - An integer representing the expiration date of the cookie in UNIX timestamp format (Default is 0).
* $path - A string containing the path on which the cookie is valid (Default is "").
* $domain - A string containing the domain on which the cookie is valid (Default is "").
* $secure - A boolean that tells the browser to only send the cookie over HTTPS (Default is false).
* $httponly - A boolean that tells the browser whether or not Javascript should be able to access the cookie's value (Default is false).

Returns: Nothing.

This old global function sends a cookie to the browser.  To expire a cookie, call the function with `$expires` set to a low value (e.g. 1).  This function corrects `$domain` issues that are encountered when using cookies.

BB_RegisterPropertyFormHandler($mode, $callback)
------------------------------------------------

Access:  _internal_

Parameters:

* $mode - A string containing one of "init", "field_string", "field_type", "table_row", "cleanup", or "finalize".
* $callback - A valid callback function for the specified mode.  The callback function must accept the correct number and type of inputs.

Returns:  Nothing.

This internal function is not relevant to modern Admin Pack and was short-lived in its usefulness.

This function is a wrapper for `FlexForms::RegisterFormHandler()`.  Call that function instead.

BB_PropertyForm($options)
-------------------------

Access:  _internal_ global

Parameters:

* $options - An array containing information about the forms part of the page to generate.

Returns:  Nothing.

This internal function generates the form portion of a `BB_GeneratePage()` call.  Most of the functionality has migrated to FlexForms.  What remains is wrapper code specific to handling legacy Admin Pack code and features.

BB_CreateSecurityToken($name, $extra = "")
------------------------------------------

Access:  global

Parameters:

* $action - A string containing the future target action request variable.
* $extra - A string or an array containing extra information or an empty string (Default is "").

Returns:  A string containing a security token.

This global function is used to generate security tokens to defend against XSS/CSRF attacks.  Security tokens are used later with CheckSecurityToken(), which verifies that the security token sent by the web browser matches before continuing.

This function is a wrapper for `$bb_flexforms->CreateSecurityToken()`.

BB_IsSecExtraOpt($opt)
----------------------

Access:  global

Parameters:

* $opt - A string containing an option to verify as being used to generate the security token.

Returns:  A boolean indicating whether or not the option is valid.

This global function verifies that `$_REQUEST["sec_extra"]` exists and contains the specified option.  This is an advanced feature that is rarely used.

This function is a wrapper for `$bb_flexforms->IsSecExtraOpt()`.

BB_ProcessPageToken($name)
--------------------------

Access:  global

Parameters:

* $name - A string containing the request variable to check the security token against.

Returns:  Nothing.

This global function verifies the required "sec_t" and optional "sec_extra" request variables against the internally calculated security token.  If the action exists but the security token is missing or does not precisely match, the application will immediately exit with an "Invalid security token" message.

This function defends against XSS/CSRF attacks.  Never write advanced form code without proper XSS/CSRF defenses.

If the specified action exists and passes the security check, the specified action is assigned to the internal automatic nonce tracker.  This is later used during `FlexForms::Generate()` for automatic form looping and to reduce error-prone issues from copying and pasting code.

This function is a wrapper for `$bb_flexforms->CheckSecurityToken()`.

BB_GetBackURL($query = array(), $fullrequest = false, $protocol = "")
---------------------------------------------------------------------

Access:  global

Parameters:

* $query - An array containing extra query string parameters (Default is array()).
* $fullrequest - A boolean to call BB_GetFullRequestURLBase() if true, BB_GetRequestURLBase() otherwise (Default is false).
* $protocol - A string containing the protocol to pass to BB_GetFullRequestURLBase() (Default is "").

Returns:  A string containing a URL.

This old global function is called by `BB_RedirectPage()` to determine where to redirect the browser.

Not intended for direct use in new applications.

BB_RedirectPage($msgtype = "", $msg = "", $query = array())
-----------------------------------------------------------

Access:  global

Parameters:

* $msgtype - A string containing one of 'error', 'success', or 'info' (Default is "").
* $msg - A string containing a message to display to the user on the redirected page (Default is "").
* $query - An array containing name/value pairs to be used as the query string (Default is array()).

Returns:  Nothing.

This global function exits upon sending the appropriate `Location` header to redirect the browser.  If the `$query` option is an empty array, then the call will return to the starting screen of the application.

When `$msg` is specified, it should be kept short and to the point as the string is sent as part of the URL.  If an empty string is passed for `$msgtype` when `$msg` is specified, the default `$msgtype` becomes 'info'.

BB_SetPageMessage($msgtype, $msg, $field = false)
-------------------------------------------------

Access:  global

Parameters:

* $msgtype - A string containing one of "error", "success", or "info".
* $msg - A string containing a message to display to the user on the redirected page.
* $field - A string containing a field name to also display the error message (Default is false).

Returns:  Nothing.

This global function sets the message to display when generating the page.  Typically used for error messages that get passed through for the user to correct some mistake.

The `$bb_errors` global is updated with the error message to display with the relevant field.  This feature only works when `$msgtype` is "error".

BB_GetPageMessageType()
-----------------------

Access:  global

Parameters: None.

Returns:  A string containing one of "error", "success", "info", or an empty string.

This global function gets the current message type.  Can be used to determine if any errors occurred in previous code.

BB_NormalizeFiles($key)
-----------------------

Access:  global

Parameters:

* $key - A string containing the file input parameter to look for.

Returns:  An array of standard arrays of information about associated files.

This global function determines if the request file input parameter exists and returns a validated multidimensional array of information.  This function handles common functionality for the PHP `$_FILES` array (e.g. calling `is_uploaded_file()`) and reduces code complexity especially when processing multidimensional file submissions.

This function is a wrapper for `FlexForms::NormalizeFiles()`.

BB_GetValue($key, $default)
---------------------------

Access:  global

Parameters:

* $key - A string containing the input parameter to look for.
* $default - The default value to use if the request input parameter is not found.

Returns:  The value of the request input parameter or the default value.

This global function determines if the request input parameter exists and returns it.  If it doesn't exist, the default value is returned.  The direct use of this function is no longer considered to be best practice since FlexForms supports a "default" key-value pair for each field.  Less code equates to fewer problems.

This function is a wrapper for `FlexForms::GetValue()`.

BB_SelectValues($data)
----------------------

Access:  global

Parameters:

* $data - An array of values.

Returns:  The array of values made into keys mapped to true.

This global function takes in an input array of values and prepares them for use as values for a "select" key in a field.  The direct use of this function is no longer considered to be best practice since FlexForms supports a "default" key-value pair for each field.  Less code equates to fewer problems.

This function is a wrapper for `FlexForms::GetSelectValues()`.

BB_ProcessInfoDefaults($info, $defaults)
----------------------------------------

Access:  global

Parameters:

* $info - An array to merge defaults into if keys are missing.
* $defaults - An array containing default key-value pairs.

Returns:  A merged array of key-value pairs.

This global function merges missing defaults into an array of information.  This function is useful for loading serialized data from a database into a consistent format.  The use of this type of approach reduces potential code breakage when new fields are added.

This function is a wrapper for `FlexForms::ProcessInfoDefaults()`.

BB_SetNestedPathValue(&$data, $pathparts, $val)
-----------------------------------------------

Access:  global

Parameters:

* $data - A multidimensional array of information.
* $pathparts - An array representing the path in $data to walk down.
* $val - The new value at the location in $data specified by $pathparts.

Returns:  Nothing.

This global function updates the input data array with the specified value.  The provided functionality can be useful for manipulating complex array structures.  The FlexForms charts module allows for custom options to be passed to C3.js via this function.

This function is a wrapper for `FlexForms::SetNestedPathValue()`.

BB_GetIDDiff($origids, $newids)
-------------------------------

Access:  global

Parameters:

* $origids - An array containing key-value pairs of original ID mapped to information.
* $newids - An array containing key-value pairs of new ID mapped to information.

Returns:  An array containing the IDs added and removed.

This global function can be used to reduce the number of operations that need to be performed to make necessary changes to a database.  For example, a table that links two tables together via IDs and the user is allowed to add and remove links.  The naive approach is remove all links and then add the ones that the user wants.  This function could be used to minimize the number of DELETE FROM and INSERT INTO statements that are executed to just those that are needed.

This function is a wrapper for `FlexForms::GetIDDiff()`.

BB_InitLayouts()
----------------

Access:  _internal_ global

Parameters:  None.

Returns:  Nothing.

This internal global function initializes missing Admin Pack HTML layouts.  The following global variables are created if they don't exist:  `$bb_page_layout`, `$bb_page_layout_no_menu`, `$bb_menu_layout`, `$bb_menu_item_layout`, `$bb_message_layout`, `$bb_page_layout_bulkedit`.

BB_GeneratePage($title, $menuopts, $contentopts)
------------------------------------------------

Access:  global

Parameters:

* $title - A string containing the title of the page.  `$bb_rootname` is automatically prepended to the title.
* $menuopts - An array containing sections and links to be used for the menu.
* $contentopts - An array containing options to pass to `BB_PropertyForm()`.

Returns: Nothing.

Generates the entire HTML page and writes the content onto the output stream that gets sent to the browser.

BB_GenerateBulkEditPage($title, $contentopts)
---------------------------------------------

Access:  global

Parameters:

* $title - A string containing the title of the page.  `$bb_rootname` is automatically prepended to the title.
* $contentopts - An array containing options for the bulk edit page.

Returns:  Nothing.

Generates an entire bulk edit HTML page and writes the content onto the output stream that gets sent to the browser.

The `$contentopts` array consists of various options:

* title - A string containing the title of the page.  Overrides `$title` when specified.
* topbarhtml - A string containing the HTML to use for the top bar.
* bottombarhtml - A string containing the HTML to use for the bottom bar.
* initialhtml - A string containing the HTML to use for the initial content area.
* javascript - A string containing the Javascript to embed for the page to function.
* items_callback - A valid callback function to load more items.  The callback function must accept no parameters - callback().
* items - An array containing items for the item selection area.

Each item in the "items" option consists of:

* id - A string containing a unique identifier for the item.  Must be safe for use as a HTML ID attribute.
* class - A string containing a class for the item (e.g. for CSS styling).
* onlick - A string containing Javascript code to execute when the item is clicked.
* htmldisplay - A string containing HTML to display for the item.  Overrides the "display" option.
* display - A string containing the text to display for the item.
