---
title: Window.open()
slug: Web/API/Window/open
tags:
  - API
  - DOM
  - Method
  - NeedsMarkupWork
  - NeedsUpdate
  - Reference
  - Window
  - open
browser-compat: api.Window.open
---
<div>{{APIRef}}</div>

<p>The {{domxref("Window")}} interface's <strong><code>open()</code></strong> method loads
  the specified resource into the new or existing browsing context (window,
  {{HTMLElement("iframe")}} or tab) with the specified name. If the name doesn't exist,
  then a new browsing context is opened in a new tab or a new window, and the specified
  resource is loaded into it.</p>

<div class="notecard note">
   <p><strong>Note:</strong> For brevity, this document will generally use the term "window" as a
   shorthand for "a browsing context in a tab or window".</p>
</div>

<h2 id="Syntax">Syntax</h2>

<pre
  class="brush: js">var window = window.open(url, windowName, [windowFeatures]);</pre>

<h3 id="Parameters">Parameters</h3>

<dl>
  <dt><code><var>url</var></code></dt>
  <dd>A {{domxref("DOMString")}} indicating the URL of the resource to be loaded. This can
    be a path or URL to an HTML page, image file, or any other resource that is supported
    by the browser. If the empty string ("") is specified as <code><var>url</var></code>,
    a blank page is opened into the targeted browsing context.</dd>
  <dt><code><var>windowName</var></code> {{optional_inline}}</dt>
  <dd>A {{domxref("DOMString")}} specifying the name of the browsing context (window,
    {{HTMLElement("iframe")}} or tab) into which to load the specified resource; if the
    name doesn't indicate an existing context, a new window is created and is given the
    name specified by <code><var>windowName</var></code>.<br>
    <br>
    This name can be used as the target for the <code>target</code> attribute of
    {{HTMLElement("a", "", "#attr-target")}} or {{HTMLElement("form", "",
    "#attr-target")}} elements. The name should not contain whitespace. Keep in mind that
    this will NOT be used as the window's title.
  </dd>
  <dt><code><var>windowFeatures</var></code> {{optional_inline}}</dt>
  <dd>A {{domxref("DOMString")}} containing a comma-separated list of window features
    given with their corresponding values in the form "name=value". These features include
    options such as the window's default size and position, whether or not to include
    toolbar, and so forth. There must be no whitespace in the string. See
    {{anch("Window features")}} below for documentation of each of the features that can
    be specified.
  </dd>
</dl>

<h3 id="Return_value">Return value</h3>

<p>A <code>WindowProxy</code> object, which is basically a thin wrapper for the
  {{domxref("Window")}} object representing the newly created window, and has all its
  features available. If the window couldn't be opened, the returned value is instead
  <code>null</code>. The returned reference can be used to access properties and methods
  of the new window as long as it complies with <a
    href="/en-US/docs/Web/Security/Same-origin_policy">Same-origin policy</a> security
  requirements.</p>

<h2 id="Description">Description</h2>

<p>The <code>open()</code> method creates a new secondary browser window, similar to
  choosing New Window from the File menu. The <code><var>url</var></code> parameter
  specifies the URL to be fetched and loaded in the new window. If
  <code><var>url</var></code> is an empty string, then a new blank, empty window (URL
  <code>about:blank</code>) is created with the default toolbars of the main window.</p>

<p>Note that remote URLs won't load immediately. When <code>window.open()</code> returns,
  the window always contains <code>about:blank</code>. The actual fetching of the URL is
  deferred and starts after the current script block finishes executing. The window
  creation and the loading of the referenced resource are done asynchronously.</p>

<h2 id="Examples">Examples</h2>

<pre class="brush:js">var windowObjectReference;
var windowFeatures = "menubar=yes,location=yes,resizable=yes,scrollbars=yes,status=yes";

function openRequestedPopup() {
  windowObjectReference = window.open("http://www.cnn.com/", "CNN_WindowName", windowFeatures);
}</pre>

<pre class="brush:js">var windowObjectReference;

function openRequestedPopup() {
  windowObjectReference = window.open(
    "http://www.domainname.ext/path/ImageFile.png",
    "DescriptiveWindowName",
    "resizable,scrollbars,status"
  );
}</pre>

<p>If a window with the name already exists, then <code><var>url</var></code> is loaded
  into the <em>existing</em> window. In this case the return value of the method is the
  existing window and <code><var>windowFeatures</var></code> is ignored. Providing an
  empty string for <code><var>url</var></code> is a way to get a reference to an open
  window by its name without changing the window's location. Otherwise the call to
  <code>window.open()</code> will just create a new window.</p>

<p>To open a <em>new</em> window on every call of <code>window.open()</code>, use the
  special value <code>_blank</code> for <code><var>windowName</var></code>.</p>

<h2 id="Window_features">Window features</h2>

<p><code><var>windowFeatures</var></code> is an optional string containing a
  comma-separated list of requested features of the new window. After a window is opened,
  JavaScript can't be used to change the features. If <code><var>windowName</var></code>
  does not specify an existing window and the <code><var>windowFeatures</var></code>
  parameter is not provided (or if the <code><var>windowFeatures</var></code> parameter is
  an empty string), then the new secondary window will render the default toolbars of the
  main window.</p>

<div class="notecard note">
  <p><strong>Note:</strong> In some browsers, users can override the
    <code><var>windowFeatures</var></code> settings and enable (or prevent the disabling
    of) features. Further, control of some window features is available only on some browsers
    and platforms (See <a href="#popup_condition">popup condition</a> section)
  </p>
</div>

<h3 id="Position_and_size_features">Position and size features</h3>

<p><code><var>windowFeatures</var></code> parameter can specify the position and size of
  the new window.</p>

<p><a href="#note_on_position_and_dimension_error_correction">Note on position and
    dimension error correction</a></p>

<h4 id="Position">Position</h4>

<div class="notecard warning">
  <p><strong>Warning:</strong> If only one of them is specified, the behavior is implementation-dependent, and web
    author should not rely on it.</p>
</div>

<dl>
  <dt><code>left</code> or <code>screenX</code></dt>
  <dd>Specifies the distance the new window is placed from the left side of the work area
    for applications of the user's operating system to the leftmost border (resizing
    handle) of the browser window. The new window cannot be initially positioned
    offscreen.</dd>
  <dt><code>top</code> or <code>screenY</code></dt>
  <dd>Specifies the distance the new window is placed from the top side of the work area
    for applications of the user's operating system to the topmost border (resizing
    handle) of the browser window. The new window cannot be initially positioned
    offscreen.</dd>
</dl>

<p>If the <code><var>windowFeatures</var></code> parameter is non-empty and if no position
  features are defined, then the left and top coordinates of the new window dimension will
  be 22 pixels from where the most recently rendered window was. An offset is universally
  implemented by browser manufacturers (it is 29 pixels in IE6 SP2 with the default theme)
  and its purpose is to help users to notice new windows opening. If the most recently
  used window was maximized, then there is no offset: the new window will be maximized as
  well.</p>

<h4 id="Size">Size</h4>

<div class="notecard warning">
  <p><strong>Warning:</strong> If only one of them is specified, the behavior is implementation-dependent, and web
    author should not rely on it.</p>
</div>

<dl>
  <dt><code>width</code> or <code>innerWidth</code></dt>
  <dd>Specifies the width of the content area, viewing area of the new secondary window in
    pixels. The width value includes the width of the vertical scrollbar if present. The
    width value does not include the sidebar if it is expanded. The minimum required value
    is 100.</dd>
  <dt><code>height</code> or <code>innerHeight</code></dt>
  <dd>Specifies the height of the content area, viewing area of the new secondary window
    in pixels. The height value includes the height of the horizontal scrollbar if
    present. The height value does not include other UI parts such as location bar, title
    bar, tab bar, etc. The minimum required value is 100.</dd>
</dl>

<p>If the <code><var>windowFeatures</var></code> parameter is non-empty and no size
  features are defined, then the new window dimensions will be the same as the dimensions
  of the most recently rendered window.</p>

<h3 id="Browser-dependent_size_features">Browser-dependent size features</h3>

<div class="notecard warning">
  <p><strong>Warning:</strong> Do not use them.</p>
</div>

<dl>
  <dt><code>outerWidth</code> {{deprecated_inline}} (only on Firefox, obsolete from Firefox 80)</dt>
  <dd>Specifies the width of the whole browser window in pixels. This
    <code>outerWidth</code> value includes the window vertical scrollbar (if present) and
    left and right window resizing borders.</dd>
  <dt><code>outerHeight</code> {{deprecated_inline}} (only on Firefox, obsolete from Firefox 80)</dt>
  <dd>Specifies the height of the whole browser window in pixels. This
    <code>outerHeight</code> value includes any/all present toolbar, window horizontal
    scrollbar (if present) and top and bottom window resizing borders. Minimal required
    value is 100.</dd>
</dl>

<h3 id="Toolbar_and_UI_parts_features">Toolbar and UI parts features</h3>

<div class="notecard warning">
  <p><strong>Warning:</strong> In modern browsers (Firefox 76 or newer, Google Chrome, Safari, Chromium Edge), the
    following features are just a condition for whether to open popup or not. See <a
      href="#popup_condition">popup condition</a> section.</p>
</div>

<p>The following features control the visibility of each UI parts, All features can be set
  to <code>yes</code> or <code>1</code>, or just be present to be on. Set them to
  <code>no</code> or <code>0</code>, or in most cases just omit them, to be off.</p>

<p>Example: <code>status=yes</code>, <code>status=1</code>, and <code>status</code> have
  identical results.</p>

<dl>
  <dt><code>menubar</code></dt>
  <dd>If this feature is on, then the new secondary window renders the menubar.<br>
    <br>
    If <code><var>windowFeatures</var></code> is non-empty, <code>menubar</code> defaults
    to off.
  </dd>
  <dt><code>toolbar</code></dt>
  <dd>If this feature is on, then the new secondary window renders the toolbar buttons
    (Back, Forward, Reload, Stop buttons).<br>
    <br>
    In addition to the toolbar buttons, Firefox (before 76) will render the Tab Bar if it
    is visible, present in the parent window. (If this feature is set to off, all toolbars
    in the window will be invisible).<br>
    <br>
    If <code><var>windowFeatures</var></code> is non-empty, <code>toolbar</code> defaults
    to off.
  </dd>
  <dt><code>location</code></dt>
  <dd>If this feature is on, then the new secondary window renders the location bar or the
    address bar.<br>
    <br>
    If <code><var>windowFeatures</var></code> is non-empty, <code>location</code> defaults
    to off.
  </dd>
  <dt><code>status</code></dt>
  <dd>If this feature is on, then the new secondary window has a status bar.<br>
    <br>
    If <code><var>windowFeatures</var></code> is non-empty, <code>status</code> defaults
    to off.
  </dd>
  <dt><code>resizable</code></dt>
  <dd>If this feature is on, the new secondary window will be resizable.<br>
    <br>
    If <code><var>windowFeatures</var></code> is non-empty, <code>resizable</code>
    defaults to on.
    <div class="notecard note">
      <p><strong>Note:</strong> For accessibility reasons, it is strongly recommended to
        set this feature always on</p>
    </div>
  </dd>
  <dt><code>scrollbars</code></dt>
  <dd>If this feature is on, the new secondary window will show horizontal and/or vertical
    scrollbar(s) if the document doesn't fit into the window's viewport.<br>
    <br>
    If <code><var>windowFeatures</var></code> is non-empty, <code>scrollbars</code>
    defaults to off.<br>
    <br>
    See <a href="#note_on_scrollbars">note on scrollbars</a>.
    <div class="notecard note">
      <p><strong>Note:</strong> For accessibility reasons, it is strongly encouraged to set
        this feature always on.</p>
    </div>
  </dd>
</dl>

<h3 id="Window_functionality_features">Window functionality features</h3>

<dl>
  <dt><code>noopener</code></dt>
  <dd>
    <p>If this feature is set, the newly-opened window will open as normal, except that it
    will not have access back to the originating window (via {{domxref("Window.opener")}}
    — it returns <code>null</code>). In addition, the <code>window.open()</code> call will
    also return <code>null</code>, so the originating window will not have access to the
    new one either. This is useful for preventing untrusted sites opened via
    <code>window.open()</code> from tampering with the originating window, and vice versa.</p>
    <p>Note that when <code>noopener</code> is used, nonempty target names other than
    <code>_top</code>, <code>_self</code>, and <code>_parent</code> are all treated like
    <code>_blank</code> in terms of deciding whether to open a new window/tab.</p>
    <p>This is supported in modern browsers including Chrome, and Firefox 52+. See
    <code><a href="/en-US/docs/Web/HTML/Link_types#noopener">rel="noopener"</a></code> for
    more information and for browser compatibility details, including information about
    ancillary effects.</p>
  </dd>
  <dt><code>noreferrer</code></dt>
  <dd>If this feature is set, the request to load the content located at the specified URL
    will be loaded with the request's <code>referrer</code> set to
    <code>noreferrer</code>; this prevents the request from sending the URL of the page
    that initiated the request to the server where the request is sent. In addition,
    setting this feature also automatically sets
    <code>noopener</code>. See
    <code><a href="/en-US/docs/Web/HTML/Link_types#noreferrer">rel="noreferrer"</a></code>
    for additional details and compatibility information. Firefox introduced support for
    <code>noreferrer</code> in Firefox 68.</dd>
</dl>

<h2 id="Best_practices">Best practices</h2>

<pre class="brush:js">&lt;script type="text/javascript"&gt;
var windowObjectReference = null; // global variable

function openFFPromotionPopup() {
  if(windowObjectReference == null || windowObjectReference.closed)
  /* if the pointer to the window object in memory does not exist
     or if such pointer exists but the window was closed */

  {
    windowObjectReference = window.open("http://www.spreadfirefox.com/",
   "PromoteFirefoxWindowName", "resizable,scrollbars,status");
    /* then create it. The new window will be created and
       will be brought on top of any other window. */
  }
  else
  {
    windowObjectReference.focus();
    /* else the window reference must exist and the window
       is not closed; therefore, we can bring it back on top of any other
       window with the focus() method. There would be no need to re-create
       the window or to reload the referenced resource. */
  };
}
&lt;/script&gt;

(...)

&lt;p&gt;&lt;a
 href="http://www.spreadfirefox.com/"
 target="PromoteFirefoxWindowName"
 onclick="openFFPromotionPopup(); return false;"
 title="This link will create a new window or will re-use an already opened one"
&gt;Promote Firefox adoption&lt;/a&gt;&lt;/p&gt;
</pre>

<p>The above code solves a few usability problems related to links opening secondary
  window. The purpose of the <code>return false</code> in the code is to cancel default
  action of the link: if the onclick event handler is executed, then there is no need to
  execute the default action of the link. But if javascript support is disabled or
  non-existent on the user's browser, then the onclick event handler is ignored and the
  browser loads the referenced resource in the target frame or window that has the name
  "PromoteFirefoxWindowName". If no frame nor window has the name
  "PromoteFirefoxWindowName", then the browser will create a new window and will name it
  "PromoteFirefoxWindowName".</p>

<p>More reading on the use of the target attribute:</p>

<p><a href="https://www.w3.org/TR/html401/present/frames.html#h-16.3.2">HTML 4.01 Target
    attribute specifications</a></p>

<p><a href="https://www.htmlhelp.com/faq/html/links.html#new-window">How do I create a link
    that opens a new window?</a></p>

<p>You can also parameterize the function to make it versatile, functional in more
  situations, therefore re-usable in scripts and webpages:</p>

<pre class="brush:js">&lt;script type="text/javascript"&gt;
var windowObjectReference = null; // global variable

function openRequestedPopup(url, windowName) {
  if(windowObjectReference == null || windowObjectReference.closed) {
    windowObjectReference = window.open(url, windowName,
           "resizable,scrollbars,status");
  } else {
    windowObjectReference.focus();
  };
}
&lt;/script&gt;

(...)

&lt;p&gt;&lt;a
 href="http://www.spreadfirefox.com/"
 target="PromoteFirefoxWindow"
 onclick="openRequestedPopup(this.href, this.target); return false;"
 title="This link will create a new window or will re-use an already opened one"
&gt;Promote Firefox adoption&lt;/a&gt;&lt;/p&gt;
</pre>

<p>You can also make such function able to open only 1 secondary window and to reuse such
  single secondary window for other links in this manner:</p>

<pre class="brush:js">&lt;script type="text/javascript"&gt;
var windowObjectReference = null; // global variable
var PreviousUrl; /* global variable that will store the
                    url currently in the secondary window */

function openRequestedSinglePopup(url) {
  if(windowObjectReference == null || windowObjectReference.closed) {
    windowObjectReference = window.open(url, "SingleSecondaryWindowName",
         "resizable,scrollbars,status");
  } else if(PreviousUrl != url) {
    windowObjectReference = window.open(url, "SingleSecondaryWindowName",
      "resizable=yes,scrollbars=yes,status=yes");
    /* if the resource to load is different,
       then we load it in the already opened secondary window and then
       we bring such window back on top/in front of its parent window. */
    windowObjectReference.focus();
  } else {
    windowObjectReference.focus();
  };

  PreviousUrl = url;
  /* explanation: we store the current url in order to compare url
     in the event of another call of this function. */
}
&lt;/script&gt;

(...)

&lt;p&gt;&lt;a
 href="http://www.spreadfirefox.com/"
 target="SingleSecondaryWindowName"
 onclick="openRequestedSinglePopup(this.href); return false;"
 title="This link will create a new window or will re-use an already opened one"
&gt;Promote Firefox adoption&lt;/a&gt;&lt;/p&gt;

&lt;p&gt;&lt;a
 href="https://www.mozilla.org/support/firefox/faq"
 target="SingleSecondaryWindowName"
 onclick="openRequestedSinglePopup(this.href); return false;"
 title="This link will create a new window or will re-use an already opened one"
&gt;Firefox FAQ&lt;/a&gt;&lt;/p&gt;
</pre>

<h2 id="FAQ">FAQ</h2>

<dl>
  <dt>How can I prevent the confirmation message asking the user whether they want to
    close the window?</dt>
  <dd>
    <p>You cannot. <strong>New windows not opened by javascript cannot as a rule be closed
      by JavaScript.</strong> The JavaScript Console in Mozilla-based browsers will report
    the warning message:
    <code>"Scripts may not close windows that were not opened by script."</code> Otherwise
    the history of URLs visited during the browser session would be lost.</p>
    <p>More on the {{domxref("window.close()")}} method.</p>
  </dd>
  <dt>How can I bring back the window if it is minimized or behind another window?</dt>
  <dd>First check for the existence of the window object reference of such window and if
    it exists and if it has not been closed, then use the <a
      href="/en-US/docs/Web/API/Window/focus">focus()</a> method. There is no other reliable
    way. You can examine an <a href="#best_practices">example explaining how to use the
      focus() method</a>.</dd>
  <dt>How do I force a maximized window?</dt>
  <dd>You cannot. All browser manufacturers try to make the opening of new secondary
    windows noticed by users and noticeable by users to avoid confusion, to avoid
    disorienting users.</dd>
  <dt>How do I turn off window resizability or remove toolbars?</dt>
  <dd>You cannot force this. Users with Mozilla-based browsers have absolute control over
    window functionalities like resizability, scrollability and toolbars presence via user
    preferences in <code>about:config</code>. Since your users are the ones who are
    supposed to use such windows (and not you, being the web author), the best is to avoid
    interfering with their habits and preferences. We recommend to always set the
    resizability and scrollbars presence (if needed) to yes to insure accessibility to
    content and usability of windows. This is in the best interests of both the web author
    and the users.</dd>
  <dt>How do I resize a window to fit its content?</dt>
  <dd>
    <p>You cannot reliably because the users can prevent the window from being resized by
    setting <code>dom.disable_window_move_resize</code> to <code>true</code> in
    <code>about:config</code> or by editing accordingly their <a
      href="https://www.mozilla.org/support/firefox/edit#user">user.js file</a>.</p>
    <p>In general, users usually disable moving and resizing of existing windows because
    allowing authors' scripts to do so has been abused overwhelmingly in the past and the
    rare scripts that do not abuse such feature are often wrong, inaccurate when resizing
    the window. 99% of all those scripts disable window resizability and disable
    scrollbars when in fact they should enable both of these features to allow a cautious
    and sane fallback mechanism if their calculations are wrong.</p>
    <p>The window method <a href="/en-US/docs/Web/API/Window/sizeToContent">sizeToContent()</a>
    can also be disabled. Moving and resizing a window remotely on the user's screen via
    script will very often annoy the users, will disorient the user, and will be wrong at
    best. The web author expects to have full control of (and can decide about) every
    position and size aspects of the users' browser window ... which is not true.</p>
  </dd>
  <dt>How do I know whether a window I opened is still open?</dt>
  <dd>You can test for the existence of the window object reference which is the returned
    value in case of success of the window.open() call and then verify that
    <var>windowObjectReference</var>.closed return value is <var>false</var>.</dd>
  <dt>How can I tell when my window was blocked by a popup blocker?</dt>
  <dd>With the built-in popup blockers, you have to check the return value of
    <code>window.open()</code>: it will be <code>null</code> if the window wasn't allowed
    to open. However, for most other popup blockers, there is no reliable way.</dd>
  <dt>What is the JavaScript relationship between the main window and the secondary
    window?</dt>
  <dd>The <code>window.open()</code> method gives a main window a reference to a secondary
    window; the <a href="/en-US/docs/Web/API/Window/opener">opener</a> property gives a
    secondary window a reference to its main window.</dd>
  <dt>I cannot access the properties of the new secondary window. I always get an error in
    the javascript console saying "Error: uncaught
    exception: Permission denied to get property
    &lt;property_name or method_name&gt;. Why is that?</dt>
  <dd>
    <p>It is because of the cross-domain script security restriction (also referred as the
    "Same Origin Policy"). A script loaded in a window (or frame) from a distinct origin
    (domain name) <strong>cannot get nor set</strong> properties of another window (or
    frame) or the properties of any of its HTML objects coming from another distinct
    origin (domain name). Therefore, before executing a script targeting a secondary
    window, the browser in the main window will verify that the secondary window has the
    same domain name.</p>
    <p>More reading on the cross-domain script security restriction: <a
      href="https://www.mozilla.org/projects/security/components/same-origin.html"
     >http://www.mozilla.org/projects/secu...me-origin.html</a></p>
   </dd>
</dl>

<h2 id="Usability_issues">Usability issues</h2>

<h3 id="Avoid_resorting_to_window.open">Avoid resorting to window.open()</h3>

<p>Generally speaking, it is preferable to avoid resorting to <code>window.open()</code>
  for several reasons:</p>

<ul>
  <li>Most modern desktop browsers offer tab-browsing, and tab-capable browser users
    overall prefer opening new tabs than opening new windows in a majority of webpage
    situations.</li>
  <li>Most modern browsers offers a popup-blocking feature.</li>
  <li>Users can be using browser-built-in feature or extensions include opening a link in
    a new window or not, in the same window, in a new tab or not, in "background" or not.
    Coding carelessly to open new windows can no longer be assured of success, cannot
    succeed by force and, if it does, it will annoy a majority of users.</li>
  <li>New windows can have menubar missing, scrollbars missing, status bar missing, window
    resizability disabled, etc.; new tabs cannot be missing those functionalities or
    toolbars (or at least, the toolbars that are present by default). Therefore,
    tab-browsing is preferred by a lot of users because the normal user-interface of the
    browser window they prefer is kept intact, remains stable.</li>
  <li>Opening new windows, even with reduced features, uses considerably a lot of the
    user's system resources (cpu, RAM) and involves considerably a lot of coding in the
    source code (security management, memory management, various code branchings sometimes
    quite complex, window frame/chrome/toolbars building, window positioning and sizing,
    etc.). Opening new tabs is less demanding on the user's system resources (and faster
    to achieve) than opening new windows.</li>
</ul>

<h3 id="Offer_to_open_a_link_in_a_new_window_using_these_guidelines">Offer to open a link
  in a new window, using these guidelines</h3>

<p>If you want to offer to open a link in a new window, then follow tested and
  recommendable usability and accessibility guidelines:</p>

<h4 id="Never_use_this_form_of_code_for_links_&lt;a_hrefjavascriptwindow.open..._...>">
  Never use this form of code for links: &lt;a href="javascript:window.open(...)" ...&gt;
</h4>

<p>"javascript:" links break accessibility and usability of webpages in every browser.</p>

<ul>
  <li>"javascript:" pseudo-links become dysfunctional when javascript support is disabled
    or inexistent. Several corporations allow their employees to surf on the web but under
    strict security policies: no javascript enabled, no java, no activeX, no Flash. For
    various reasons (security, public access, text browsers, etc..), about 5% to 10% of
    users on the web surf with javascript disabled.</li>
  <li>"javascript:" links will interfere with advanced features in tab-capable browsers:
    eg. middle-click on links, Ctrl+click on links, tab-browsing features in extensions,
    etc.</li>
  <li>"javascript:" links will interfere with the process of indexing webpages by search
    engines.</li>
  <li>"javascript:" links interfere with assistive technologies (e.g. voice browsers) and
    several web-aware applications (e.g. <abbr
      title="Personal Digital Assistant">PDAs</abbr> and mobile browsers).</li>
  <li>"javascript:" links also interfere with "mouse gestures" features implemented in
    browsers.</li>
  <li>Protocol scheme "javascript:" will be reported as an error by link validators and
    link checkers.</li>
</ul>

<p><strong>Further reading:</strong></p>

<ul>
  <li><a href="https://www.useit.com/alertbox/20021223.html">Top Ten Web-Design Mistakes of
      2002</a>, 6. JavaScript in Links, Jakob Nielsen, December 2002</li>
  <li><a
      href="https://www.evolt.org/article/Links_and_JavaScript_Living_Together_in_Harmony/17/20938/">Links
      &amp; JavaScript Living Together in Harmony</a>, Jeff Howden, February 2002</li>
  <li><a href="https://jibbering.com/faq/#FAQ4_24">comp.lang.javascript newsgroup
      discussion FAQ on "javascript:" links</a></li>
</ul>

<h4 id="Never_use_&lt;a_href_onclickwindow.open...>">Never use
  <code>&lt;a href="#" onclick="window.open(...);"&gt;</code></h4>

<p>Such pseudo-link also breaks accessibility of links. <strong>Always use a real URL for
    the href attribute value</strong> so that if javascript support is disabled or
  inexistent or if the user agent does not support opening of secondary window (like
  MS-Web TV, text browsers, etc), then such user agents will still be able to load the
  referenced resource according to its default mode of opening/handling a referenced
  resource. This form of code also interferes with advanced features in tab-capable
  browsers: eg. middle-click on links, Ctrl+click on links, Ctrl+Enter on links, "mouse
  gestures" features.</p>

<h4 id="Always_identify_links_that_will_create_or_will_re-use_a_new_secondary_window">
  Always identify links that will create (or will re-use) a new, secondary window</h4>

<p>Identify links that will open new windows in a way that helps navigation for users by
  coding the title attribute of the link, by adding an icon at the end of the link or by
  coding the cursor accordingly.</p>

<p>The purpose is to warn users in advance of context changes to minimize confusion on the
  user's part: changing the current window or popping up new windows can be very
  disorienting to users (Back toolbar button is disabled).</p>

<blockquote>
  <p>"Users often don't notice that a new window has opened, especially if they are using
    a small monitor where the windows are maximized to fill up the screen. So a user who
    tries to return to the origin will be confused by a grayed out <em>Back</em>
    button."<br>
    quote from <a href="https://www.useit.com/alertbox/990530.html">The Top Ten
      <em>New</em> Mistakes of Web Design</a>: 2. Opening New Browser Windows, Jakob
    Nielsen, May 1999</p>
</blockquote>

<p>When extreme changes in context are explicitly identified before they occur, then the
  users can determine if they wish to proceed or so they can be prepared for the change:
  not only they will not be confused or feel disoriented, but more experienced users can
  better decide how to open such links (in a new window or not, in the same window, in a
  new tab or not, in "background" or not).</p>

<p><strong>References</strong></p>

<ul>
  <li>"If your link spawns a new window, or causes another windows to 'pop up' on your
    display, or move the focus of the system to a new FRAME or Window, then the nice thing
    to do is to tell the user that something like that will happen." <a
      href="https://www.w3.org/WAI/wcag-curric/sam77-0.htm">World Wide Web Consortium
      Accessibility Initiative regarding popups</a></li>
  <li>"Use link titles to provide users with a preview of where each link will take them,
    before they have clicked on it." <a
      href="https://www.useit.com/alertbox/991003.html">Ten Good Deeds in Web Design</a>,
    Jakob Nielsen, October 1999</li>
  <li><a href="https://www.useit.com/alertbox/980111.html">Using Link Titles to Help Users
      Predict Where They Are Going</a>, Jakob Nielsen, January 1998</li>
</ul>

<h4 id="Always_use_the_target_attribute">Always use the target attribute</h4>

<p>If javascript support is disabled or non-existent, then the user agent will create a
  secondary window accordingly or will render the referenced resource according to its
  handling of the target attribute: e.g. some user agents that cannot create new windows,
  like MS Web TV, will fetch the referenced resource and append it at the end of the
  current document. The goal and the idea is to try to provide - <strong>not
    impose</strong> - to the user a way to open the referenced resource, a mode of opening
  the link. Your code should not interfere with the features of the browser at the
  disposal of the user and your code should not interfere with the final decision resting
  with the user.</p>

<h4 id="Do_not_use_target_blank">Do not use <code>target="_blank"</code></h4>

<p>Always provide a meaningful name to your target attribute and try to reuse such target
  attribute in your page so that a click on another link may load the referenced resource
  in an already created and rendered window (therefore speeding up the process for the
  user) and therefore justifying the reason (and user system resources, time spent) for
  creating a secondary window in the first place. Using a single target attribute value
  and reusing it in links is much more user resources friendly as it only creates one
  single secondary window, which is recycled. On the other hand, using "_blank" as the
  target attribute value will create several new and unnamed windows on the user's desktop
  that cannot be recycled, reused. In any case, if your code is well done, it should not
  interfere with the user's final choice but rather merely offer them more choices, more
  ways to open links and more power to the tool they are using (a browser).</p>

<h2 id="Glossary">Glossary</h2>

<dl>
  <dt>Opener window, parent window, main window, first window</dt>
  <dd>Terms often used to describe or to identify the same window. It is the window from
    which a new window will be created. It is the window on which the user clicked a link
    that leads to the creation of another, new window.</dd>
  <dt>Sub-window, child window, secondary window, second window</dt>
  <dd>Terms often used to describe or to identify the same window. It is the new window
    that was created.</dd>
  <dt>Unrequested popup windows</dt>
  <dd>Script-initiated windows opening automatically without the user's consent.</dd>
</dl>

<h2 id="Specifications">Specifications</h2>

{{Specifications}}

<h2 id="Browser_compatibility">Browser compatibility</h2>

<p>{{Compat}}</p>

<h2 id="Notes">Notes</h2>

<h3 id="Popup_condition">Popup condition</h3>

<p>Most modern browsers (listed below) don't allow web content to control the visibility
  of UI parts separately.</p>

<ul>
  <li>Firefox 76 or newer</li>
  <li>Google Chrome</li>
  <li>Safari</li>
  <li>Chromium Edge</li>
</ul>

<p>UI-related items of <code><var>windowFeatures</var></code> are used as a condition to
  whether opening a popup or a new tab, or a new window, and UI parts visibility of each
  of them is fixed.</p>

<p>The condition is implementation-dependent and not guaranteed to be stable.</p>

<h3 id="Note_on_scrollbars">Note on scrollbars</h3>

<p>When content overflows window viewport dimensions, then scrollbar(s) (or some scrolling
  mechanism) are necessary to ensure that content can be accessed by users. Content can
  overflow window dimensions for several reasons that are outside the control of web
  authors:</p>

<ul>
  <li>user resizes the window</li>
  <li>user increases the text size of fonts via View/Text Zoom (%) menuitem in Mozilla or
    View/Text Size/Increase or Decrease in Firefox</li>
  <li>user sets a minimum font size for pages that is bigger than the font-size the web
    author requested. People over 40 years old or with particular viewing habit or reading
    preference often set a minimal font size in Mozilla-based browsers.</li>
  <li>web author is not aware of default margin (and/or border and/or padding) values
    applying to root element or body node in various browsers and various browser versions
  </li>
  <li>user uses an user stylesheet (<a
      href="https://www.mozilla.org/support/firefox/edit#content">userContent.css in
      Mozilla-based browsers</a>) for his viewing habits that increases document box
    dimensions (margin, padding, default font size)</li>
  <li>user can customize individually the size (height or width) of most toolbars via
    operating system settings. E.g. window resizing borders, height of browser titlebar,
    menubar, scrollbars, font size are entirely customizable by the user in Windows XP
    operating system. These toolbars dimensions can also be set via browser themes and
    skins or by operating system themes</li>
  <li>web author is unaware that the user default browser window has custom toolbar(s) for
    specific purpose(s); e.g.: prefs bar, web developer bar, accessibility toolbar, popup
    blocking and search toolbar, multi-feature toolbar, etc.</li>
  <li>user uses assistive technologies or add-on features that modify the operating
    system's work area for applications: e.g. MS-Magnifier</li>
  <li>user repositions and/or resizes directly or indirectly the operating system's work
    area for applications: e.g. user resizes the Windows taskbar, user positions the
    Windows taskbar on the left side (arabic language based) or right side (Hebrew
    language), user has a permanent MS-Office quick launch toolbar, etc.</li>
  <li>some operating system (Mac OS X) forces presence of toolbars that can then fool the
    web author's anticipations, calculations of the effective dimensions of the browser
    window</li>
</ul>

<h3 id="Note_on_position_and_dimension_error_correction">Note on position and dimension
  error correction</h3>

<p>Requested position and requested dimension values in the <var>features</var> list will
  not be honored and <strong>will be corrected</strong> if any of such requested value
  does not allow the entire browser window to be rendered within the work area for
  applications of the user's operating system. <strong>No part of the new window can be
    initially positioned offscreen. This is by default in all Mozilla-based browser
    releases.</strong></p>

<p><a href="https://msdn2.microsoft.com/en-us/library/ms997645.aspx#xpsp_topic5">MSIE 6 SP2
    has a similar error correction mechanism</a> but it is not activated by default in all
  security levels: a security setting can disable such error correction mechanism.</p>

<h2 id="Tutorials">Tutorials</h2>

<ul>
  <li><a href="https://www.infimum.dk/HTML/JSwindows.html">JavaScript windows
      (tutorial)</a> by Lasse Reichstein Nielsen</li>
  <li><a href="https://accessify.com/features/tutorials/the-perfect-popup/">The perfect
      pop-up (tutorial)</a> by Ian Lloyd</li>
  <li><a href="https://www.gtalbot.org/FirefoxSection/Popup/PopupAndFirefox.html">Popup
      windows and Firefox (interactive demos)</a> by Gérard Talbot</li>
</ul>

<h2 id="References">References</h2>

<ul>
  <li><a href="https://www.cs.tut.fi/~jkorpela/www/links.html">Links Want To Be Links</a>
    by Jukka K. Korpela</li>
  <li><a
      href="https://www.evolt.org/article/Links_and_JavaScript_Living_Together_in_Harmony/17/20938/">Links
      &amp; JavaScript Living Together in Harmony</a> by Jeff Howden</li>
</ul>

<h2 id="Others">Others</h2>

<ul>
  <li><a href="/en-US/docs/Web/API/Window/open/Obsolete_features">Obsolete features</a>
  </li>
  <li><a href="/en-US/docs/Web/API/Window/open/Privileged_features">Privileged
      features</a></li>
</ul>