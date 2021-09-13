---
title: Using XMLHttpRequest
slug: Web/API/XMLHttpRequest/Using_XMLHttpRequest
tags:
  - AJAX
  - AJAXfile
  - Advanced
  - DOM
  - Guide
  - HTTP
  - JXON
  - MakeBrowserAgnostic
  - Tutorial
  - XHR
  - XML
  - XMLHttpRequest
---
<div>{{APIRef("XMLHttpRequest")}}</div>

<p>In this guide, we'll take a look at how to use
    {{domxref("XMLHttpRequest")}} to issue <a href="/en-US/docs/Web/HTTP">HTTP</a>
    requests in order to exchange data between the web site and a server.</p>

<p>Examples
  of both common and more obscure use cases for <code>XMLHttpRequest</code> are included.
</p>

<p>To send an HTTP request, create an <code>XMLHttpRequest</code> object, open a URL, and
  send the request. After the transaction completes, the object will contain useful
  information such as the response body and the <a href="/en-US/docs/Web/HTTP/Status">HTTP
    status</a> of the result.</p>

<pre class="brush: js">function reqListener () {
  console.log(this.responseText);
}

var oReq = new XMLHttpRequest();
oReq.addEventListener("load", reqListener);
oReq.open("GET", "http://www.example.org/example.txt");
oReq.send();</pre>

<h2 id="Types_of_requests">Types of requests</h2>

<p>A request made via <code>XMLHttpRequest</code> can fetch the data in one of two ways,
  asynchronously or synchronously. The type of request is dictated by the optional
  <code>async</code> argument (the third argument) that is set on the
  {{domxref("XMLHttpRequest.open()")}} method. If this argument is <code>true</code> or
  not specified, the <code>XMLHttpRequest</code> is processed asynchronously, otherwise
  the process is handled synchronously. A detailed discussion and demonstrations of these
  two types of requests can be found on the <a
    href="/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests">synchronous
    and asynchronous requests</a> page. Do not use synchronous requests outside Web
  Workers.</p>

<div class="note"><p><strong>Note:</strong> Starting with Gecko 30.0 {{ geckoRelease("30.0")
  }}, synchronous requests on the main thread have been deprecated due to the negative
  effects to the user experience.</p></div>

<div class="notecard note">
  <p><strong>Note:</strong> The constructor function
  <code>XMLHttpRequest</code> isn't limited to only XML documents. It starts with
  <strong>"XML"</strong> because when it was created the main format that was originally
  used for Asynchronous Data Exchange was XML.</p>
</div>

<h2 id="Handling_responses">Handling responses</h2>

<p>There are several types of <a href="https://xhr.spec.whatwg.org/">response
    attributes</a> defined by the living standard specification for the
  {{domxref("XMLHttpRequest.XMLHttpRequest", "XMLHttpRequest()")}} constructor. These tell
  the client making the <code>XMLHttpRequest</code> important information about the status
  of the response. Some cases where dealing with non-text response types may involve some
  manipulation and analysis are outlined in the following sections.</p>

<h3 id="Analyzing_and_manipulating_the_responseXML_property">Analyzing and manipulating
  the responseXML property</h3>

<p>If you use <code>XMLHttpRequest</code> to get the content of a remote XML document, the
  {{domxref("XMLHttpRequest.responseXML", "responseXML")}} property will be a DOM object
  containing a parsed XML document. This could prove difficult to manipulate and analyze.
  There are four primary ways of analyzing this XML document:</p>

<ol>
  <li>Using <a href="/en-US/docs/Web/XPath">XPath</a> to address (or point to) parts of
    it.</li>
  <li>Manually <a href="/en-US/docs/Web/Guide/Parsing_and_serializing_XML">Parsing and
      serializing XML</a> to strings or objects.</li>
  <li>Using {{domxref("XMLSerializer")}} to serialize <strong>DOM trees to strings or to
      files</strong>.</li>
  <li>{{jsxref("RegExp")}} can be used if you always know the content of the XML document
    beforehand. You might want to remove line breaks, if you use <code>RegExp</code> to
    scan with regard to line breaks. However, this method is a "last resort" since if the
    XML code changes slightly, the method will likely fail.</li>
</ol>

<div class="notecard note">
  <p><strong>Note:</strong> <code>XMLHttpRequest</code> can now interpret HTML for you
    using the {{domxref("XMLHttpRequest.responseXML", "responseXML")}} property. Read the
    article about <a href="/en-US/docs/Web/API/XMLHttpRequest/HTML_in_XMLHttpRequest">HTML
      in XMLHttpRequest</a> to learn how to do this.</p>
</div>

<h3 id="Processing_a_responseText_property_containing_an_HTML_document">Processing a
  responseText property containing an HTML document</h3>

<p>If you use <code>XMLHttpRequest</code> to get the content of a remote HTML webpage, the
  {{domxref("XMLHttpRequest.responseText", "responseText")}} property is a string
  containing the raw HTML. This could prove difficult to manipulate and analyze. There are
  three primary ways to analyze and parse this raw HTML string:</p>

<ol>
  <li>Use the <code>XMLHttpRequest.responseXML</code> property as covered in the article
    <a href="/en-US/docs/Web/API/XMLHttpRequest/HTML_in_XMLHttpRequest">HTML in
      XMLHttpRequest</a>.</li>
  <li>Inject the content into the body of a <a
      href="/en-US/docs/Web/API/DocumentFragment">document fragment</a> via
    <code>fragment.body.innerHTML</code> and traverse the DOM of the fragment.</li>
  <li>{{jsxref("RegExp")}} can be used if you always know the content of the HTML
    <code>responseText</code> beforehand. You might want to remove line breaks, if you use
    RegExp to scan with regard to linebreaks. However, this method is a "last resort"
    since if the HTML code changes slightly, the method will likely fail.</li>
</ol>

<h2 id="Handling_binary_data">Handling binary data</h2>

<p>Although {{domxref("XMLHttpRequest")}} is most commonly used to send and receive
  textual data, it can be used to send and receive binary content. There are several well
  tested methods for coercing the response of an <code>XMLHttpRequest</code> into sending
  binary data. These involve utilizing the {{domxref("XMLHttpRequest.overrideMimeType",
  "overrideMimeType()")}} method on the <code>XMLHttpRequest</code> object and is a
  workable solution.</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();
oReq.open("GET", url);
// retrieve data unprocessed as a binary string
oReq.overrideMimeType("text/plain; charset=x-user-defined");
/* ... */
</pre>

<p>However, more modern techniques are available, since the
  {{domxref("XMLHttpRequest.responseType", "responseType")}} attribute now supports a
  number of additional content types, which makes sending and receiving binary data much
  easier.</p>

<p>For example, consider this snippet, which uses the <code>responseType</code> of
  "<code>arraybuffer</code>" to fetch the remote content into a {{jsxref("ArrayBuffer")}}
  object, which stores the raw binary data.</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.onload = function(e) {
  var arraybuffer = oReq.response; // not responseText
  /* ... */
}
oReq.open("GET", url);
oReq.responseType = "arraybuffer";
oReq.send();</pre>

<p>For more examples check out the <a
    href="/en-US/docs/Web/API/XMLHttpRequest/Sending_and_Receiving_Binary_Data">Sending and
    Receiving Binary Data</a> page</p>

<h2 id="Monitoring_progress">Monitoring progress</h2>

<p><code>XMLHttpRequest</code> provides the ability to listen to various events that can
  occur while the request is being processed. This includes periodic progress
  notifications, error notifications, and so forth.</p>

<p>Support for DOM {{event("progress")}} event monitoring of <code>XMLHttpRequest</code>
  transfers follows the <a
    href="https://xhr.spec.whatwg.org/#interface-progressevent">specification for progress
    events</a>: these events implement the {{domxref("ProgressEvent")}} interface. The
  actual events you can monitor to determine the state of an ongoing transfer are:</p>

<dl>
  <dt>{{event("progress")}}</dt>
  <dd>The amount of data that has been retrieved has changed.</dd>
  <dt>{{event("load")}}</dt>
  <dd>The transfer is complete; all data is now in the <code>response</code></dd>
</dl>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.addEventListener("progress", updateProgress);
oReq.addEventListener("load", transferComplete);
oReq.addEventListener("error", transferFailed);
oReq.addEventListener("abort", transferCanceled);

oReq.open();

// ...

// progress on transfers from the server to the client (downloads)
function updateProgress (oEvent) {
  if (oEvent.lengthComputable) {
    var percentComplete = oEvent.loaded / oEvent.total * 100;
    // ...
  } else {
    // Unable to compute progress information since the total size is unknown
  }
}

function transferComplete(evt) {
  console.log("The transfer is complete.");
}

function transferFailed(evt) {
  console.log("An error occurred while transferring the file.");
}

function transferCanceled(evt) {
  console.log("The transfer has been canceled by the user.");
}</pre>

<p>Lines 3-6 add event listeners for the various events that are sent while performing a
  data transfer using <code>XMLHttpRequest</code>.</p>

<div class="note"><p><strong>Note:</strong> You need to add the event listeners before
  calling <code>open()</code> on the request. Otherwise the <code>progress</code> events
  will not fire.</p></div>

<p>The progress event handler, specified by the <code>updateProgress()</code> function in
  this example, receives the total number of bytes to transfer as well as the number of
  bytes transferred so far in the event's <code>total</code> and <code>loaded</code>
  fields. However, if the <code>lengthComputable</code> field is false, the total length
  is not known and will be zero.</p>

<p>Progress events exist for both download and upload transfers. The download events are
  fired on the <code>XMLHttpRequest</code> object itself, as shown in the above sample.
  The upload events are fired on the <code>XMLHttpRequest.upload</code> object, as shown
  below:</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.upload.addEventListener("progress", updateProgress);
oReq.upload.addEventListener("load", transferComplete);
oReq.upload.addEventListener("error", transferFailed);
oReq.upload.addEventListener("abort", transferCanceled);

oReq.open();
</pre>

<div class="note"><p><strong>Note:</strong> Progress events are not available for the
  <code>file:</code> protocol.</p></div>

<div class="note">
  <p><strong>Note:</strong> Starting in {{Gecko("9.0")}}, progress events can now be
    relied upon to come in for every chunk of data received, including the last chunk in
    cases in which the last packet is received and the connection closed before the
    progress event is fired. In this case, the progress event is automatically fired when
    the load event occurs for that packet. This lets you now reliably monitor progress by
    only watching the "progress" event.</p>
</div>

<div class="note">
  <p><strong>Note:</strong> As of {{Gecko("12.0")}}, if your progress event is called with
    a <code>responseType</code> of "moz-blob", the value of response is a
    {{domxref("Blob")}} containing the data received so far.</p>
</div>

<p>One can also detect all three load-ending conditions (<code>abort</code>,
  <code>load</code>, or <code>error</code>) using the <code>loadend</code> event:</p>

<pre class="brush:js">req.addEventListener("loadend", loadEnd);

function loadEnd(e) {
  console.log("The transfer finished (although we don't know if it succeeded or not).");
}
</pre>

<p>Note there is no way to be certain, from the information received by the
  <code>loadend</code> event, as to which condition caused the operation to terminate;
  however, you can use this to handle tasks that need to be performed in all
  end-of-transfer scenarios.</p>

<h2 id="Submitting_forms_and_uploading_files">Submitting forms and uploading files</h2>

<p>Instances of <code>XMLHttpRequest</code> can be used to submit forms in two ways:</p>

<ul>
  <li>using only AJAX</li>
  <li>using the {{domxref("FormData")}} API</li>
</ul>

<p>Using the <code>FormData</code> API is the simplest and fastest, but has the
  disadvantage that data collected can not be <a
    href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify">stringified</a>.<br>
  Using only AJAX is more complex, but typically more flexible and powerful.</p>

<h3 id="Using_nothing_but_XMLHttpRequest">Using nothing but <code>XMLHttpRequest</code>
</h3>

<p>Submitting forms without the <code>FormData</code> API does not require other APIs for
  most use cases. The only case where you need an additional API is <strong>if you want to
    upload one or more files</strong>, where you use the {{domxref("FileReader")}} API.
</p>

<h4 id="A_brief_introduction_to_the_submit_methods">A brief introduction to the submit
  methods</h4>

<p>An html {{ HTMLElement("form") }} can be sent in four ways:</p>

<ul>
  <li>using the <code>POST</code> method and setting the <code>enctype</code> attribute to
    <code>application/x-www-form-urlencoded</code> (default);</li>
  <li>using the <code>POST</code> method and setting the <code>enctype</code> attribute to
    <code>text/plain</code>;</li>
  <li>using the <code>POST</code> method and setting the <code>enctype</code> attribute to
    <code>multipart/form-data</code>;</li>
  <li>using the <code>GET</code> method (in this case the <code>enctype</code> attribute
    will be ignored).</li>
</ul>

<p>Now, consider the submission of a form containing only two fields, named
  <code>foo</code> and <code>baz</code>. If you are using the <code>POST</code> method the
  server will receive a string similar to one of the following three examples, depending
  on the encoding type you are using:</p>

<ul>
  <li>
    <p>Method: <code>POST</code>; Encoding type:
      <code>application/x-www-form-urlencoded</code> (default):</p>

    <pre class="brush:plain">Content-Type: application/x-www-form-urlencoded

foo=bar&amp;baz=The+first+line.%0D%0AThe+second+line.%0D%0A</pre>
  </li>
  <li>
    <p>Method: <code>POST</code>; Encoding type: <code>text/plain</code>:</p>

    <pre class="brush:plain">Content-Type: text/plain

foo=bar
baz=The first line.
The second line.</pre>
  </li>
  <li>
    <p>Method: <code>POST</code>; Encoding type:
      <code><a href="/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types#multipartform-data">multipart/form-data</a></code>:
    </p>

    <pre class="brush:plain">Content-Type: multipart/form-data; boundary=---------------------------314911788813839

-----------------------------314911788813839
Content-Disposition: form-data; name="foo"

bar
-----------------------------314911788813839
Content-Disposition: form-data; name="baz"

The first line.
The second line.

-----------------------------314911788813839--</pre>
  </li>
</ul>

<p>However, if you are using the <code>GET</code> method, a string like the following will
  be added to the URL:</p>

<pre
  class="brush:plain">?foo=bar&amp;baz=The%20first%20line.%0AThe%20second%20line.</pre>

<h4 id="A_little_vanilla_framework">A little vanilla framework</h4>

<p>All these effects are done automatically by the web browser whenever you submit a
  {{HTMLElement("form")}}. If you want to perform the same effects using JavaScript you
  have to instruct the interpreter about <em>everything</em>. Therefore, how to send forms
  in <em>pure</em> AJAX is too complex to be explained here in detail. For this reason,
  here we place <strong>a complete (yet didactic) framework</strong>, able to use all four
  ways to <em>submit</em>, and to <strong>upload files</strong>:</p>


  <pre class="brush: html">&lt;!doctype html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /&gt;
&lt;title&gt;Sending forms with pure AJAX &amp;ndash; MDN&lt;/title&gt;
&lt;script type="text/javascript"&gt;

"use strict";

/*\
|*|
|*|  :: XMLHttpRequest.prototype.sendAsBinary() Polyfill ::
|*|
|*|  https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest#sendAsBinary()
\*/

if (!XMLHttpRequest.prototype.sendAsBinary) {
  XMLHttpRequest.prototype.sendAsBinary = function(sData) {
    var nBytes = sData.length, ui8Data = new Uint8Array(nBytes);
    for (var nIdx = 0; nIdx &lt; nBytes; nIdx++) {
      ui8Data[nIdx] = sData.charCodeAt(nIdx) &amp; 0xff;
    }
    /* send as ArrayBufferView...: */
    this.send(ui8Data);
    /* ...or as ArrayBuffer (legacy)...: this.send(ui8Data.buffer); */
  };
}

/*\
|*|
|*|  :: AJAX Form Submit Framework ::
|*|
|*|  https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest/Using_XMLHttpRequest
|*|
|*|  This framework is released under the GNU Public License, version 3 or later.
|*|  https://www.gnu.org/licenses/gpl-3.0-standalone.html
|*|
|*|  Syntax:
|*|
|*|   AJAXSubmit(HTMLFormElement);
\*/

var AJAXSubmit = (function () {

  function ajaxSuccess () {
    /* console.log("AJAXSubmit - Success!"); */
    console.log(this.responseText);
    /* you can get the serialized data through the "submittedData" custom property: */
    /* console.log(JSON.stringify(this.submittedData)); */
  }

  function submitData (oData) {
    /* the AJAX request... */
    var oAjaxReq = new XMLHttpRequest();
    oAjaxReq.submittedData = oData;
    oAjaxReq.onload = ajaxSuccess;
    if (oData.technique === 0) {
      /* method is GET */
      oAjaxReq.open("get", oData.receiver.replace(/(?:\?.*)?$/,
          oData.segments.length &gt; 0 ? "?" + oData.segments.join("&amp;") : ""), true);
      oAjaxReq.send(null);
    } else {
      /* method is POST */
      oAjaxReq.open("post", oData.receiver, true);
      if (oData.technique === 3) {
        /* enctype is multipart/form-data */
        var sBoundary = "---------------------------" + Date.now().toString(16);
        oAjaxReq.setRequestHeader("Content-Type", "multipart\/form-data; boundary=" + sBoundary);
        oAjaxReq.sendAsBinary("--" + sBoundary + "\r\n" +
            oData.segments.join("--" + sBoundary + "\r\n") + "--" + sBoundary + "--\r\n");
      } else {
        /* enctype is application/x-www-form-urlencoded or text/plain */
        oAjaxReq.setRequestHeader("Content-Type", oData.contentType);
        oAjaxReq.send(oData.segments.join(oData.technique === 2 ? "\r\n" : "&amp;"));
      }
    }
  }

  function processStatus (oData) {
    if (oData.status &gt; 0) { return; }
    /* the form is now totally serialized! do something before sending it to the server... */
    /* doSomething(oData); */
    /* console.log("AJAXSubmit - The form is now serialized. Submitting..."); */
    submitData (oData);
  }

  function pushSegment (oFREvt) {
    this.owner.segments[this.segmentIdx] += oFREvt.target.result + "\r\n";
    this.owner.status--;
    processStatus(this.owner);
  }

  function plainEscape (sText) {
    /* How should I treat a text/plain form encoding?
       What characters are not allowed? this is what I suppose...: */
    /* "4\3\7 - Einstein said E=mc2" ----&gt; "4\\3\\7\ -\ Einstein\ said\ E\=mc2" */
    return sText.replace(/[\s\=\\]/g, "\\$&amp;");
  }

  function SubmitRequest (oTarget) {
    var nFile, sFieldType, oField, oSegmReq, oFile, bIsPost = oTarget.method.toLowerCase() === "post";
    /* console.log("AJAXSubmit - Serializing form..."); */
    this.contentType = bIsPost &amp;&amp; oTarget.enctype ? oTarget.enctype : "application\/x-www-form-urlencoded";
    this.technique = bIsPost ?
        this.contentType === "multipart\/form-data" ? 3 : this.contentType === "text\/plain" ? 2 : 1 : 0;
    this.receiver = oTarget.action;
    this.status = 0;
    this.segments = [];
    var fFilter = this.technique === 2 ? plainEscape : escape;
    for (var nItem = 0; nItem &lt; oTarget.elements.length; nItem++) {
      oField = oTarget.elements[nItem];
      if (!oField.hasAttribute("name")) { continue; }
      sFieldType = oField.nodeName.toUpperCase() === "INPUT" ? oField.getAttribute("type").toUpperCase() : "TEXT";
      if (sFieldType === "FILE" &amp;&amp; oField.files.length &gt; 0) {
        if (this.technique === 3) {
          /* enctype is multipart/form-data */
          for (nFile = 0; nFile &lt; oField.files.length; nFile++) {
            oFile = oField.files[nFile];
            oSegmReq = new FileReader();
            /* (custom properties:) */
            oSegmReq.segmentIdx = this.segments.length;
            oSegmReq.owner = this;
            /* (end of custom properties) */
            oSegmReq.onload = pushSegment;
            this.segments.push("Content-Disposition: form-data; name=\"" +
                oField.name + "\"; filename=\"" + oFile.name +
                "\"\r\nContent-Type: " + oFile.type + "\r\n\r\n");
            this.status++;
            oSegmReq.readAsBinaryString(oFile);
          }
        } else {
          /* enctype is application/x-www-form-urlencoded or text/plain or
             method is GET: files will not be sent! */
          for (nFile = 0; nFile &lt; oField.files.length;
              this.segments.push(fFilter(oField.name) + "=" + fFilter(oField.files[nFile++].name)));
        }
      } else if ((sFieldType !== "RADIO" &amp;&amp; sFieldType !== "CHECKBOX") || oField.checked) {
        /* NOTE: this will submit _all_ submit buttons. Detecting the correct one is non-trivial. */
        /* field type is not FILE or is FILE but is empty */
        this.segments.push(
          this.technique === 3 ? /* enctype is multipart/form-data */
            "Content-Disposition: form-data; name=\"" + oField.name + "\"\r\n\r\n" + oField.value + "\r\n"
          : /* enctype is application/x-www-form-urlencoded or text/plain or method is GET */
            fFilter(oField.name) + "=" + fFilter(oField.value)
        );
      }
    }
    processStatus(this);
  }

  return function (oFormElement) {
    if (!oFormElement.action) { return; }
    new SubmitRequest(oFormElement);
  };

})();

&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;h1&gt;Sending forms with pure AJAX&lt;/h1&gt;

&lt;h2&gt;Using the GET method&lt;/h2&gt;

&lt;form action="register.php" method="get" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h2&gt;Using the POST method&lt;/h2&gt;
&lt;h3&gt;Enctype: application/x-www-form-urlencoded (default)&lt;/h3&gt;

&lt;form action="register.php" method="post" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: text/plain&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="text/plain"
    onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      Your name: &lt;input type="text" name="user" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Your message:&lt;br /&gt;
      &lt;textarea name="message" cols="40" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: multipart/form-data&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="multipart/form-data"
    onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Upload example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;&lt;br /&gt;
      Sex:
      &lt;input id="sex_male" type="radio" name="sex" value="male" /&gt;
      &lt;label for="sex_male"&gt;Male&lt;/label&gt;
      &lt;input id="sex_female" type="radio" name="sex" value="female" /&gt;
      &lt;label for="sex_female"&gt;Female&lt;/label&gt;&lt;br /&gt;
      Password: &lt;input type="password" name="secret" /&gt;&lt;br /&gt;
      What do you prefer:
      &lt;select name="image_type"&gt;
        &lt;option&gt;Books&lt;/option&gt;
        &lt;option&gt;Cinema&lt;/option&gt;
        &lt;option&gt;TV&lt;/option&gt;
      &lt;/select&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Post your photos:
      &lt;input type="file" multiple name="photos[]"&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input id="vehicle_bike" type="checkbox" name="vehicle[]" value="Bike" /&gt;
      &lt;label for="vehicle_bike"&gt;I have a bike&lt;/label&gt;&lt;br /&gt;
      &lt;input id="vehicle_car" type="checkbox" name="vehicle[]" value="Car" /&gt;
      &lt;label for="vehicle_car"&gt;I have a car&lt;/label&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Describe yourself:&lt;br /&gt;
      &lt;textarea name="description" cols="50" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;/body&gt;
&lt;/html&gt;</pre>

<p>To test this, create a page named <strong>register.php</strong> (which is the
  <code>action</code> attribute of these sample forms), and put the following
  <em>minimalistic</em> content:</p>

<pre class="brush: php">&lt;?php
/* register.php */

header("Content-type: text/plain");

/*
NOTE: You should never use `print_r()` in production scripts, or
otherwise output client-submitted data without sanitizing it first.
Failing to sanitize can lead to cross-site scripting vulnerabilities.
*/

echo ":: data received via GET ::\n\n";
print_r($_GET);

echo "\n\n:: Data received via POST ::\n\n";
print_r($_POST);

echo "\n\n:: Data received as \"raw\" (text/plain encoding) ::\n\n";
if (isset($HTTP_RAW_POST_DATA)) { echo $HTTP_RAW_POST_DATA; }

echo "\n\n:: Files received ::\n\n";
print_r($_FILES);

</pre>

<p>The syntax to activate this script is:</p>

<pre class="brush: js">AJAXSubmit(myForm);</pre>

<div class="note"><p><strong>Note:</strong> This framework uses the {{domxref("FileReader")}}
  API to transmit file uploads. This is a recent API and is not implemented in IE9 or
  below. For this reason, the AJAX-only upload is considered <strong>an experimental
    technique</strong>. If you do not need to upload binary files, this framework works
  fine in most browsers.</p></div>

<div class="note"><p><strong>Note:</strong> The best way to send binary content is via
  {{jsxref("ArrayBuffer", "ArrayBuffers")}} or {{domxref("Blob", "Blobs")}} in conjuncton
  with the {{domxref("XMLHttpRequest.send()", "send()")}} method and possibly the
  {{domxref("FileReader.readAsArrayBuffer()", "readAsArrayBuffer()")}} method of the
  <code>FileReader</code> API. But, since the aim of this script is to work with a <a
    href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify">stringifiable</a>
  raw data, we used the {{domxref("XMLHttpRequest.sendAsBinary()", "sendAsBinary()")}}
  method in conjunction with the {{domxref("FileReader.readAsBinaryString()",
  "readAsBinaryString()")}} method of the <code>FileReader</code> API. As such, the above
  script makes sense only when you are dealing with small files. If you do not intend to
  upload binary content, consider instead using the <code>FormData</code> API.</p></div>

<div class="note"><p><strong>Note:</strong> The non-standard <code>sendAsBinary</code> method
  is considered deprecated as of Gecko 31 {{geckoRelease(31)}} and will be removed soon.
  The standard <code>send(Blob data)</code> method can be used instead.</p></div>

<h3 id="Using_FormData_objects">Using FormData objects</h3>

<p>The {{domxref("FormData")}} constructor lets you compile a
  set of key/value pairs to send using <code>XMLHttpRequest</code>. Its primary use is in
  sending form data, but can also be used independently from a form in order to transmit
  user keyed data. The transmitted data is in the same format the form's
  <code>submit()</code> method uses to send data, if the form's encoding type were set to
  "multipart/form-data". FormData objects can be utilized in a number of ways with an
  <code>XMLHttpRequest</code>. For examples, and explanations of how one can utilize
  FormData with XMLHttpRequests, see the <a
    href="/en-US/docs/Web/API/FormData/Using_FormData_Objects">Using FormData
    Objects</a> page. For didactic purposes here is <strong>a <em>translation</em> of <a
      href="#a_little_vanilla_framework">the previous example</a> transformed to use the
    <code>FormData</code> API</strong>. Note the brevity of the code:</p>

  <pre class="brush: html">&lt;!doctype html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" charset="UTF-8" /&gt;
&lt;title&gt;Sending forms with FormData &amp;ndash; MDN&lt;/title&gt;
&lt;script&gt;
"use strict";

function ajaxSuccess () {
  console.log(this.responseText);
}

function AJAXSubmit (oFormElement) {
  if (!oFormElement.action) { return; }
  var oReq = new XMLHttpRequest();
  oReq.onload = ajaxSuccess;
  if (oFormElement.method.toLowerCase() === "post") {
    oReq.open("post", oFormElement.action);
    oReq.send(new FormData(oFormElement));
  } else {
    var oField, sFieldType, nFile, sSearch = "";
    for (var nItem = 0; nItem &lt; oFormElement.elements.length; nItem++) {
      oField = oFormElement.elements[nItem];
      if (!oField.hasAttribute("name")) { continue; }
      sFieldType = oField.nodeName.toUpperCase() === "INPUT" ?
          oField.getAttribute("type").toUpperCase() : "TEXT";
      if (sFieldType === "FILE") {
        for (nFile = 0; nFile &lt; oField.files.length;
            sSearch += "&amp;" + escape(oField.name) + "=" + escape(oField.files[nFile++].name));
      } else if ((sFieldType !== "RADIO" &amp;&amp; sFieldType !== "CHECKBOX") || oField.checked) {
        sSearch += "&amp;" + escape(oField.name) + "=" + escape(oField.value);
      }
    }
    oReq.open("get", oFormElement.action.replace(/(?:\?.*)?$/, sSearch.replace(/^&amp;/, "?")), true);
    oReq.send(null);
  }
}
&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;h1&gt;Sending forms with FormData&lt;/h1&gt;

&lt;h2&gt;Using the GET method&lt;/h2&gt;

&lt;form action="register.php" method="get" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h2&gt;Using the POST method&lt;/h2&gt;
&lt;h3&gt;Enctype: application/x-www-form-urlencoded (default)&lt;/h3&gt;

&lt;form action="register.php" method="post" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: text/plain&lt;/h3&gt;

&lt;p&gt;The text/plain encoding is not supported by the FormData API.&lt;/p&gt;

&lt;h3&gt;Enctype: multipart/form-data&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="multipart/form-data"
    onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Upload example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;&lt;br /&gt;
      Sex:
      &lt;input id="sex_male" type="radio" name="sex" value="male" /&gt;
      &lt;label for="sex_male"&gt;Male&lt;/label&gt;
      &lt;input id="sex_female" type="radio" name="sex" value="female" /&gt;
      &lt;label for="sex_female"&gt;Female&lt;/label&gt;&lt;br /&gt;
      Password: &lt;input type="password" name="secret" /&gt;&lt;br /&gt;
      What do you prefer:
      &lt;select name="image_type"&gt;
        &lt;option&gt;Books&lt;/option&gt;
        &lt;option&gt;Cinema&lt;/option&gt;
        &lt;option&gt;TV&lt;/option&gt;
      &lt;/select&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Post your photos:
      &lt;input type="file" multiple name="photos[]"&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input id="vehicle_bike" type="checkbox" name="vehicle[]" value="Bike" /&gt;
      &lt;label for="vehicle_bike"&gt;I have a bike&lt;/label&gt;&lt;br /&gt;
      &lt;input id="vehicle_car" type="checkbox" name="vehicle[]" value="Car" /&gt;
      &lt;label for="vehicle_car"&gt;I have a car&lt;/label&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Describe yourself:&lt;br /&gt;
      &lt;textarea name="description" cols="50" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>


<div class="note"><p><strong>Note:</strong> As we said,<strong> {{domxref("FormData")}}
    objects are not <a
      href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify">stringifiable</a>
    objects</strong>. If you want to stringify a submitted data, use <a
    href="#a_little_vanilla_framework">the previous <em>pure</em>-AJAX example</a>. Note
  also that, although in this example there are some <code>file</code> {{
  HTMLElement("input") }} fields, <strong>when you submit a form through the
    <code>FormData</code> API you do not need to use the {{domxref("FileReader")}} API
    also</strong>: files are automatically loaded and uploaded.</p></div>

<h2 id="Get_last_modified_date">Get last modified date</h2>

<pre class="brush: js">function getHeaderTime () {
  console.log(this.getResponseHeader("Last-Modified"));  /* A valid GMTString date or null */
}

var oReq = new XMLHttpRequest();
oReq.open("HEAD" /* use HEAD if you only need the headers! */, "yourpage.html");
oReq.onload = getHeaderTime;
oReq.send();</pre>

<h3 id="Do_something_when_last_modified_date_changes">Do something when last modified date
  changes</h3>

<p>Let's create two functions:</p>

<pre class="brush: js">function getHeaderTime () {
  var nLastVisit = parseFloat(window.localStorage.getItem('lm_' + this.filepath));
  var nLastModif = Date.parse(this.getResponseHeader("Last-Modified"));

  if (isNaN(nLastVisit) || nLastModif &gt; nLastVisit) {
    window.localStorage.setItem('lm_' + this.filepath, Date.now());
    isFinite(nLastVisit) &amp;&amp; this.callback(nLastModif, nLastVisit);
  }
}

function ifHasChanged(sURL, fCallback) {
  var oReq = new XMLHttpRequest();
  oReq.open("HEAD" /* use HEAD - we only need the headers! */, sURL);
  oReq.callback = fCallback;
  oReq.filepath = sURL;
  oReq.onload = getHeaderTime;
  oReq.send();
}</pre>

<p>And to test:</p>

<pre class="brush: js">/* Let's test the file "yourpage.html"... */

ifHasChanged("yourpage.html", function (nModif, nVisit) {
  console.log("The page '" + this.filepath + "' has been changed on " + (new Date(nModif)).toLocaleString() + "!");
});</pre>

<p>If you want to know <strong><em>if</em> <em>the current page</em> has changed</strong>,
  please read the article about {{domxref("document.lastModified")}}.</p>

<h2 id="Cross-site_XMLHttpRequest">Cross-site XMLHttpRequest</h2>

<p>Modern browsers support cross-site requests by implementing the <a
    href="/en-US/docs/Web/HTTP/CORS">Cross-Origin Resource Sharing</a> (CORS) standard. As
  long as the server is configured to allow requests from your web application's origin,
  <code>XMLHttpRequest</code> will work. Otherwise, an <code>INVALID_ACCESS_ERR</code>
  exception is thrown.</p>

<h2 id="Bypassing_the_cache">Bypassing the cache</h2>

<p>A cross-browser compatible approach to bypassing the cache is appending a timestamp to
  the URL, being sure to include a "?" or "&amp;" as appropriate. For example:</p>

<pre class="brush:plain">http://foo.com/bar.html -&gt; http://foo.com/bar.html?12345
http://foo.com/bar.html?foobar=baz -&gt; http://foo.com/bar.html?foobar=baz&amp;12345
</pre>

<p>As the local cache is indexed by URL, this causes every request to be unique, thereby
  bypassing the cache.</p>

<p>You can automatically adjust URLs using the following code:</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.open("GET", url + ((/\?/).test(url) ? "&amp;" : "?") + (new Date()).getTime());
oReq.send(null);</pre>

<h2 id="Security">Security</h2>

<p>{{fx_minversion_note(3, "Versions of Firefox prior to Firefox 3 allowed you to set the
  preference
  <code>capability.policy.&lt;policyname&gt;.XMLHttpRequest.open&lt;/policyname&gt;</code>
  to <code>allAccess</code> to give specific sites cross-site access. This is no longer
  supported.")}}</p>

<p>{{fx_minversion_note(5, "Versions of Firefox prior to Firefox 5 could use
  <code>netscape.security.PrivilegeManager.enablePrivilege(\"UniversalBrowserRead\");</code>
  to request cross-site access. This is no longer supported, even though it produces no
  warning and permission dialog is still presented.")}}</p>

<p>The recommended way to enable cross-site scripting is to use the
  <code>Access-Control-Allow-Origin</code> HTTP header in the response to the
  XMLHttpRequest.</p>

<h3 id="XMLHttpRequests_being_stopped">XMLHttpRequests being stopped</h3>

<p>If you conclude with an XMLHttpRequest receiving <code>status=0</code> and
  <code>statusText=null</code>, this means the request was not allowed to be performed. It
  was
  <code><a href="https://xhr.spec.whatwg.org/#dom-xmlhttprequest-unsent">UNSENT</a></code>.
  A likely cause for this is when the <a
    href="https://www.w3.org/TR/2010/CR-XMLHttpRequest-20100803/#xmlhttprequest-origin"><code>XMLHttpRequest</code>
    origin</a> (at the creation of the XMLHttpRequest) has changed when the XMLHttpRequest
  is subsequently <code>open()</code>. This case can happen, for example, when one has an
  XMLHttpRequest that gets fired on an onunload event for a window, the expected
  XMLHttpRequest is created when the window to be closed is still there, and finally
  sending the request (in other words, <code>open()</code>) when this window has lost its
  focus and another window gains focus. The most effective way to avoid this problem is to
  set a listener on the new window's {{event("activate")}} event which is set once the
  terminated window has its {{event("unload")}} event triggered.</p>

<h2 id="Workers">Workers</h2>

<p>Setting <code>overrideMimeType</code> does not work from a {{domxref("Worker")}}. See
  {{bug(678057)}} for more details. Other browsers may handle this differently.</p>

<h2 id="Specifications">Specifications</h2>

{{Specifications("api.XMLHttpRequest")}}

<h2 id="Browser_compatibility">Browser compatibility</h2>

<p>{{Compat("api.XMLHttpRequest")}}</p>

<h2 id="See_also">See also</h2>

<ol>
  <li><a href="/en-US/docs/Web/Guide/AJAX/Getting_Started">MDN AJAX introduction</a></li>
  <li><a href="/en-US/docs/Web/API/XMLHttpRequest/HTML_in_XMLHttpRequest">HTML in
      XMLHttpRequest</a></li>
  <li><a href="/en-US/docs/Web/HTTP/CORS">HTTP access control</a></li>
  <li><a
      href="/en-US/docs/Web/API/XMLHttpRequest/How_to_check_the_secruity_state_of_an_XMLHTTPRequest_over_SSL">How
      to check the security state of an XMLHTTPRequest over SSL</a></li>
  <li><a href="https://www.peej.co.uk/articles/rich-user-experience.html">XMLHttpRequest -
      REST and the Rich User Experience</a></li>
  <li><a href="https://msdn.microsoft.com/library/ms535874">Microsoft documentation</a>
  </li>
  <li><a href="https://jibbering.com/2002/4/httprequest.html">"Using the XMLHttpRequest
      Object" (jibbering.com)</a></li>
  <li><a href="https://xhr.spec.whatwg.org/">The <code>XMLHttpRequest</code> object:
      WHATWG specification</a></li>
</ol>