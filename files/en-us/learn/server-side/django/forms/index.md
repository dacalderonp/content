---
title: 'Django Tutorial Part 9: Working with forms'
slug: Learn/Server-side/Django/Forms
tags:
  - Beginner
  - CodingScripting
  - Django Forms
  - Forms
  - HTML forms
  - Learn
  - Tutorial
  - django
  - server side
---
<div> {{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Server-side/Django/authentication_and_sessions", "Learn/Server-side/Django/Testing", "Learn/Server-side/Django")}}</div>

<p>In this tutorial, we'll show you how to work with HTML Forms in Django, and, in particular, the easiest way to write forms to create, update, and delete model instances. As part of this demonstration, we'll extend the <a href="/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website">LocalLibrary</a> website so that librarians can renew books, and create, update, and delete authors using our own forms (rather than using the admin application).</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Complete all previous tutorial topics, including <a href="/en-US/docs/Learn/Server-side/Django/Authentication">Django Tutorial Part 8: User authentication and permissions</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To understand how to write forms to get information from users and update the database. To understand how the generic class-based editing views can vastly simplify creating forms for working with a single model.</td>
  </tr>
 </tbody>
</table>

<h2 id="Overview">Overview</h2>

<p>An <a href="/en-US/docs/Learn/Forms">HTML Form</a> is a group of one or more fields/widgets on a web page, which can be used to collect information from users for submission to a server. Forms are a flexible mechanism for collecting user input because there are suitable widgets for entering many different types of data, including text boxes, checkboxes, radio buttons, date pickers and so on. Forms are also a relatively secure way of sharing data with the server, as they allow us to send data in <code>POST</code> requests with cross-site request forgery protection.</p>

<p>While we haven't created any forms in this tutorial so far, we've already encountered them in the Django Admin site — for example, the screenshot below shows a form for editing one of our <a href="/en-US/docs/Learn/Server-side/Django/Models">Book</a> models, comprised of a number of selection lists and text editors.</p>

<p><img alt="Admin Site - Book Add" src="admin_book_add.png"></p>

<p>Working with forms can be complicated! Developers need to write HTML for the form, validate and properly sanitize entered data on the server (and possibly also in the browser), repost the form with error messages to inform users of any invalid fields, handle the data when it has successfully been submitted, and finally respond to the user in some way to indicate success. <em>Django Forms</em> take a lot of the work out of all these steps, by providing a framework that lets you define forms and their fields programmatically, and then use these objects to both generate the form HTML code and handle much of the validation and user interaction.</p>

<p>In this tutorial, we're going to show you a few of the ways you can create and work with forms, and in particular, how the generic editing views can significantly reduce the amount of work you need to do to create forms to manipulate your models. Along the way, we'll extend our <em>LocalLibrary</em> application by adding a form to allow librarians to renew library books, and we'll create pages to create, edit and delete books and authors (reproducing a basic version of the form shown above for editing books).</p>

<h2 id="HTML_Forms">HTML Forms</h2>

<p>First a brief overview of <a href="/en-US/docs/Learn/Forms">HTML Forms</a>. Consider a simple HTML form, with a single text field for entering the name of some "team", and its associated label:</p>

<p><img alt="Simple name field example in HTML form" src="form_example_name_field.png"></p>

<p>The form is defined in HTML as a collection of elements inside <code>&lt;form&gt;...&lt;/form&gt;</code> tags, containing at least one <code>input</code> element of <code>type="submit"</code>.</p>

<pre class="brush: html">&lt;form action="/team_name_url/" method="post"&gt;
    &lt;label for="team_name"&gt;Enter name: &lt;/label&gt;
    &lt;input id="team_name" type="text" name="name_field" value="Default name for team."&gt;
    &lt;input type="submit" value="OK"&gt;
&lt;/form&gt;</pre>

<p>While here we just have one text field for entering the team name, a form <em>may</em> have any number of other input elements and their associated labels. The field's <code>type</code> attribute defines what sort of widget will be displayed. The <code>name</code> and <code>id</code> of the field are used to identify the field in JavaScript/CSS/HTML, while <code>value</code> defines the initial value for the field when it is first displayed. The matching team label is specified using the <code>label</code> tag (see "Enter name" above), with a <code>for</code> field containing the <code>id</code> value of the associated <code>input</code>.</p>

<p>The <code>submit</code> input will be displayed as a button (by default) that can be pressed by the user to upload the data in all the other input elements in the form to the server (in this case, just the <code>team_name</code>). The form attributes define the HTTP <code>method</code> used to send the data and the destination of the data on the server (<code>action</code>):</p>

<ul>
 <li><code>action</code>: The resource/URL where data is to be sent for processing when the form is submitted. If this is not set (or set to an empty string), then the form will be submitted back to the current page URL.</li>
 <li><code>method</code>: The HTTP method used to send the data: <em>post</em> or <em>get</em>.
  <ul>
   <li>The <code>POST</code> method should always be used if the data is going to result in a change to the server's database because this can be made more resistant to cross-site forgery request attacks.</li>
   <li>The <code>GET</code> method should only be used for forms that don't change user data (e.g. a search form). It is recommended for when you want to be able to bookmark or share the URL.</li>
  </ul>
 </li>
</ul>

<p>The role of the server is first to render the initial form state — either containing blank fields or pre-populated with initial values. After the user presses the submit button, the server will receive the form data with values from the web browser and must validate the information. If the form contains invalid data, the server should display the form again, this time with user-entered data in "valid" fields and messages to describe the problem for the invalid fields. Once the server gets a request with all valid form data, it can perform an appropriate action (e.g. saving the data, returning the result of a search, uploading a file, etc.) and then notify the user.</p>

<p>As you can imagine, creating the HTML, validating the returned data, re-displaying the entered data with error reports if needed, and performing the desired operation on valid data can all take quite a lot of effort to "get right". Django makes this a lot easier, by taking away some of the heavy lifting and repetitive code!</p>

<h2 id="Django_form_handling_process">Django form handling process</h2>

<p>Django's form handling uses all of the same techniques that we learned about in previous tutorials (for displaying information about our models): the view gets a request, performs any actions required including reading data from the models, then generates and returns an HTML page (from a template, into which we pass a <em>context</em> containing the data to be displayed). What makes things more complicated is that the server also needs to be able to process data provided by the user, and redisplay the page if there are any errors.</p>

<p>A process flowchart of how Django handles form requests is shown below, starting with a request for a page containing a form (shown in green).</p>

<p><img alt="Updated form handling process doc." src="form_handling_-_standard.png"></p>

<p>Based on the diagram above, the main things that Django's form handling does are:</p>

<ol>
 <li>Display the default form the first time it is requested by the user.
  <ul>
   <li>The form may contain blank fields (e.g. if you're creating a new record), or it may be pre-populated with initial values (e.g. if you are changing a record, or have useful default initial values).</li>
   <li>The form is referred to as <em>unbound</em> at this point, because it isn't associated with any user-entered data (though it may have initial values).</li>
  </ul>
 </li>
 <li>Receive data from a submit request and bind it to the form.
  <ul>
   <li>Binding data to the form means that the user-entered data and any errors are available when we need to redisplay the form.</li>
  </ul>
 </li>
 <li>Clean and validate the data.
  <ul>
   <li>Cleaning the data performs sanitization of the input (e.g. removing invalid characters that might be used to send malicious content to the server) and converts them into consistent Python types.</li>
   <li>Validation checks that the values are appropriate for the field (e.g. are in the right date range, aren't too short or too long, etc.)</li>
  </ul>
 </li>
 <li>If any data is invalid, re-display the form, this time with any user populated values and error messages for the problem fields.</li>
 <li>If all data is valid, perform required actions (e.g. save the data, send an email, return the result of a search, upload a file, etc.)</li>
 <li>Once all actions are complete, redirect the user to another page.</li>
</ol>

<p>Django provides a number of tools and approaches to help you with the tasks detailed above. The most fundamental is the <code>Form</code> class, which simplifies both generation of form HTML and data cleaning/validation. In the next section, we describe how forms work using the practical example of a page to allow librarians to renew books.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Understanding how <code>Form</code> is used will help you when we discuss Django's more "high level" form framework classes.</p>
</div>

<h2 id="Renew-book_form_using_a_Form_and_function_view">Renew-book form using a Form and function view</h2>

<p>Next, we're going to add a page to allow librarians to renew borrowed books. To do this we'll create a form that allows users to enter a date value. We'll seed the field with an initial value 3 weeks from the current date (the normal borrowing period), and add some validation to ensure that the librarian can't enter a date in the past or a date too far in the future. When a valid date has been entered, we'll write it to the current record's <code>BookInstance.due_back</code> field.</p>

<p>The example will use a function-based view and a <code>Form</code> class. The following sections explain how forms work, and the changes you need to make to our ongoing <em>LocalLibrary</em> project.</p>

<h3 id="Form">Form</h3>

<p>The <code>Form</code> class is the heart of Django's form handling system. It specifies the fields in the form, their layout, display widgets, labels, initial values, valid values, and (once validated) the error messages associated with invalid fields. The class also provides methods for rendering itself in templates using predefined formats (tables, lists, etc.) or for getting the value of any element (enabling fine-grained manual rendering).</p>

<h4 id="Declaring_a_Form">Declaring a Form</h4>

<p>The declaration syntax for a <code>Form</code> is very similar to that for declaring a <code>Model</code>, and shares the same field types (and some similar parameters). This makes sense because in both cases we need to ensure that each field handles the right types of data, is constrained to valid data, and has a description for display/documentation.</p>

<p>Form data is stored in an application's forms.py file, inside the application directory. Create and open the file <strong>locallibrary/catalog/forms.py</strong>. To create a <code>Form</code>, we import the <code>forms</code> library, derive from the <code>Form</code> class, and declare the form's fields. A very basic form class for our library book renewal form is shown below — add this to your new file:</p>

<pre class="brush: python">from django import forms

class RenewBookForm(forms.Form):
    renewal_date = forms.DateField(help_text="Enter a date between now and 4 weeks (default 3).")
</pre>

<h4 id="Form_fields">Form fields</h4>

<p>In this case, we have a single <code><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#datefield">DateField</a></code> for entering the renewal date that will render in HTML with a blank value, the default label "<em>Renewal date:</em>", and some helpful usage text: "<em>Enter a date between now and 4 weeks (default 3 weeks).</em>" As none of the other optional arguments are specified the field will accept dates using the <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#django.forms.DateField.input_formats">input_formats</a>: YYYY-MM-DD (2016-11-06), MM/DD/YYYY (02/26/2016), MM/DD/YY (10/25/16), and will be rendered using the default <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#widget">widget</a>: <a href="https://docs.djangoproject.com/en/3.1/ref/forms/widgets/#django.forms.DateInput">DateInput</a>.</p>

<p>There are many other types of form fields, which you will largely recognize from their similarity to the equivalent model field classes: <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#booleanfield"><code>BooleanField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#charfield"><code>CharField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#choicefield"><code>ChoiceField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#typedchoicefield"><code>TypedChoiceField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#datefield"><code>DateField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#datetimefield"><code>DateTimeField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#decimalfield"><code>DecimalField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#durationfield"><code>DurationField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#emailfield"><code>EmailField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#filefield"><code>FileField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#filepathfield"><code>FilePathField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#floatfield"><code>FloatField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#imagefield"><code>ImageField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#integerfield"><code>IntegerField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#genericipaddressfield"><code>GenericIPAddressField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#multiplechoicefield"><code>MultipleChoiceField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#typedmultiplechoicefield"><code>TypedMultipleChoiceField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#nullbooleanfield"><code>NullBooleanField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#regexfield"><code>RegexField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#slugfield"><code>SlugField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#timefield"><code>TimeField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#urlfield"><code>URLField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#uuidfield"><code>UUIDField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#combofield"><code>ComboField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#multivaluefield"><code>MultiValueField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#splitdatetimefield"><code>SplitDateTimeField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#modelmultiplechoicefield"><code>ModelMultipleChoiceField</code></a>, <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#modelchoicefield"><code>ModelChoiceField</code></a>.</p>

<p>The arguments that are common to most fields are listed below (these have sensible default values):</p>

<ul>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#required">required</a>: If <code>True</code>, the field may not be left blank or given a <code>None</code> value. Fields are required by default, so you would set <code>required=False</code> to allow blank values in the form.</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#label">label</a>: The label to use when rendering the field in HTML. If a <a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#label">label</a> is not specified, Django will create one from the field name by capitalizing the first letter and replacing underscores with spaces (e.g. <em>Renewal date</em>).</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#label-suffix">label_suffix</a>: By default, a colon is displayed after the label (e.g. Renewal date<strong>:</strong>). This argument allows you to specify a different suffix containing other character(s).</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#initial">initial</a>: The initial value for the field when the form is displayed.</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#widget">widget</a>: The display widget to use.</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#help-text">help_text</a> (as seen in the example above): Additional text that can be displayed in forms to explain how to use the field.</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#error-messages">error_messages</a>: A list of error messages for the field. You can override these with your own messages if needed.</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#validators">validators</a>: A list of functions that will be called on the field when it is validated.</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#localize">localize</a>: Enables the localization of form data input (see link for more information).</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/#disabled">disabled</a>: The field is displayed but its value cannot be edited if this is <code>True</code>. The default is <code>False</code>.</li>
</ul>

<h4 id="Validation">Validation</h4>

<p>Django provides numerous places where you can validate your data. The easiest way to validate a single field is to override the method <code>clean_<strong>&lt;fieldname&gt;</strong>()</code> for the field you want to check. So for example, we can validate that entered <code>renewal_date</code> values are between now and 4 weeks by implementing <code>clean_<strong>renewal_date</strong>()</code> as shown below.</p>

<p>Update your forms.py file so it looks like this:</p>

<pre class="brush: python">import datetime

from django import forms

from django.core.exceptions import ValidationError
from django.utils.translation import ugettext_lazy as _

class RenewBookForm(forms.Form):
    renewal_date = forms.DateField(help_text="Enter a date between now and 4 weeks (default 3).")

    def clean_renewal_date(self):
        data = self.cleaned_data['renewal_date']

        # Check if a date is not in the past.
        if data &lt; datetime.date.today():
            raise ValidationError(_('Invalid date - renewal in past'))

        # Check if a date is in the allowed range (+4 weeks from today).
        if data &gt; datetime.date.today() + datetime.timedelta(weeks=4):
            raise ValidationError(_('Invalid date - renewal more than 4 weeks ahead'))

        # Remember to always return the cleaned data.
        return data</pre>

<p>There are two important things to note. The first is that we get our data using <code>self.cleaned_data['renewal_date']</code> and that we return this data whether or not we change it at the end of the function.
This step gets us the data "cleaned" and sanitized of potentially unsafe input using the default validators, and converted into the correct standard type for the data (in this case a Python <code>datetime.datetime</code> object).</p>

<p>The second point is that if a value falls outside our range we raise a <code>ValidationError</code>, specifying the error text that we want to display in the form if an invalid value is entered. The example above also wraps this text in one of <a href="https://docs.djangoproject.com/en/3.1/topics/i18n/translation/">Django's translation functions</a> <code>ugettext_lazy()</code> (imported as <code>_()</code>), which is good practice if you want to translate your site later.</p>

<div class="notecard note">
  <p><strong>Note:</strong> There are numerous other methods and examples for validating forms in <a href="https://docs.djangoproject.com/en/3.1/ref/forms/validation/">Form and field validation</a> (Django docs). For example, in cases where you have multiple fields that depend on each other, you can override the <a href="https://docs.djangoproject.com/en/3.1/ref/forms/api/#django.forms.Form.clean">Form.clean()</a> function and again raise a <code>ValidationError</code>.</p>
</div>

<p>That's all we need for the form in this example!</p>

<h3 id="URL_configuration">URL configuration</h3>

<p>Before we create our view, let's add a URL configuration for the <em>renew-books</em> page. Copy the following configuration to the bottom of <strong>locallibrary/catalog/urls.py</strong>.</p>

<pre class="brush: python">urlpatterns += [
    path('book/&lt;uuid:pk&gt;/renew/', views.renew_book_librarian, name='renew-book-librarian'),
]</pre>

<p>The URL configuration will redirect URLs with the format <strong>/catalog/book/<em>&lt;bookinstance_id&gt;</em>/renew/</strong> to the function named <code>renew_book_librarian()</code> in <strong>views.py</strong>, and send the <code>BookInstance</code> id as the parameter named <code>pk</code>. The pattern only matches if <code>pk</code> is a correctly formatted <code>uuid</code>.</p>

<div class="notecard note">
  <p><strong>Note:</strong> We can name our captured URL data "<code>pk</code>" anything we like, because we have complete control over the view function (we're not using a generic detail view class that expects parameters with a certain name). However, <code>pk</code> short for "primary key", is a reasonable convention to use!</p>
</div>

<h3 id="View">View</h3>

<p>As discussed in the <a href="#django_form_handling_process">Django form handling process</a> above, the view has to render the default form when it is first called and then either re-render it with error messages if the data is invalid, or process the data and redirect to a new page if the data is valid. In order to perform these different actions, the view has to be able to know whether it is being called for the first time to render the default form, or a subsequent time to validate data. </p>

<p>For forms that use a <code>POST</code> request to submit information to the server, the most common pattern is for the view to test against the <code>POST</code> request type (<code>if request.method == 'POST':</code>) to identify form validation requests and <code>GET</code> (using an <code>else</code> condition) to identify the initial form creation request. If you want to submit your data using a <code>GET</code> request, then a typical approach for identifying whether this is the first or subsequent view invocation is to read the form data (e.g. to read a hidden value in the form).</p>

<p>The book renewal process will be writing to our database, so, by convention, we use the <code>POST</code> request approach.
The code fragment below shows the (very standard) pattern for this sort of function view.</p>

<pre class="brush: python">import datetime

from django.shortcuts import render, get_object_or_404
from django.http import HttpResponseRedirect
from django.urls import reverse

from catalog.forms import RenewBookForm

def renew_book_librarian(request, pk):
    book_instance = get_object_or_404(BookInstance, pk=pk)

    # If this is a POST request then process the Form data
    if request.method == 'POST':

        # Create a form instance and populate it with data from the request (binding):
        form = RenewBookForm(request.POST)

        # Check if the form is valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required (here we just write it to the model due_back field)
            book_instance.due_back = form.cleaned_data['renewal_date']
            book_instance.save()

            # redirect to a new URL:
            return HttpResponseRedirect(reverse('all-borrowed') )

    # If this is a GET (or any other method) create the default form.
    else:
        proposed_renewal_date = datetime.date.today() + datetime.timedelta(weeks=3)
        form = RenewBookForm(initial={'renewal_date': proposed_renewal_date})

    context = {
        'form': form,
        'book_instance': book_instance,
    }

    return render(request, 'catalog/book_renew_librarian.html', context)</pre>

<p>First, we import our form (<code>RenewBookForm</code>) and a number of other useful objects/methods used in the body of the view function:</p>

<ul>
 <li><code><a href="https://docs.djangoproject.com/en/3.1/topics/http/shortcuts/#get-object-or-404">get_object_or_404()</a></code>: Returns a specified object from a model based on its primary key value, and raises an <code>Http404</code> exception (not found) if the record does not exist.</li>
 <li><code><a href="https://docs.djangoproject.com/en/3.1/ref/request-response/#django.http.HttpResponseRedirect">HttpResponseRedirect</a></code>: This creates a redirect to a specified URL (HTTP status code 302).</li>
 <li><code><a href="https://docs.djangoproject.com/en/3.1/ref/urlresolvers/#django.urls.reverse">reverse()</a></code>: This generates a URL from a URL configuration name and a set of arguments. It is the Python equivalent of the <code>url</code> tag that we've been using in our templates.</li>
 <li><code><a href="https://docs.python.org/3/library/datetime.html">datetime</a></code>: A Python library for manipulating dates and times.</li>
</ul>

<p>In the view, we first use the <code>pk</code> argument in <code>get_object_or_404()</code> to get the current <code>BookInstance</code> (if this does not exist, the view will immediately exit and the page will display a "not found" error).
If this is <em>not</em> a <code>POST</code> request (handled by the <code>else</code> clause) then we create the default form passing in an <code>initial</code> value for the <code>renewal_date</code> field, 3 weeks from the current date. </p>

<pre class="brush: python">    book_instance = get_object_or_404(BookInstance, pk=pk)

    # If this is a GET (or any other method) create the default form
    else:
        proposed_renewal_date = datetime.date.today() + datetime.timedelta(weeks=3)
        form = RenewBookForm(initial={'renewal_date': proposed_renewal_date})

    context = {
        'form': form,
        'book_instance': book_instance,
    }

    return render(request, 'catalog/book_renew_librarian.html', context)</pre>

<p>After creating the form, we call <code>render()</code> to create the HTML page, specifying the template and a context that contains our form. In this case, the context also contains our <code>BookInstance</code>, which we'll use in the template to provide information about the book we're renewing.</p>

<p>However, if this is a <code>POST</code> request, then we create our <code>form</code> object and populate it with data from the request. This process is called "binding" and allows us to validate the form.
We then check if the form is valid, which runs all the validation code on all of the fields — including both the generic code to check that our date field is actually a valid date and our specific form's <code>clean_renewal_date()</code> function to check the date is in the right range. </p>

<pre class="brush: python">    book_instance = get_object_or_404(BookInstance, pk=pk)

    # If this is a POST request then process the Form data
    if request.method == 'POST':

        # Create a form instance and populate it with data from the request (binding):
        form = RenewBookForm(request.POST)

        # Check if the form is valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required (here we just write it to the model due_back field)
            book_instance.due_back = form.cleaned_data['renewal_date']
            book_instance.save()

            # redirect to a new URL:
            return HttpResponseRedirect(reverse('all-borrowed') )

    context = {
        'form': form,
        'book_instance': book_instance,
    }

    return render(request, 'catalog/book_renew_librarian.html', context)</pre>

<p>If the form is not valid we call <code>render()</code> again, but this time the form value passed in the context will include error messages. </p>

<p>If the form is valid, then we can start to use the data, accessing it through the <code>form.cleaned_data</code> attribute (e.g. <code>data = form.cleaned_data['renewal_date']</code>). Here we just save the data into the <code>due_back</code> value of the associated <code>BookInstance</code> object.</p>

<div class="notecard warning">
    <p><strong>Warning:</strong> While you can also access the form data directly through the request (for example, <code>request.POST['renewal_date']</code> or <code>request.GET['renewal_date']</code> if using a GET request), this is NOT recommended. The cleaned data is sanitized, validated, and converted into Python-friendly types.</p>
</div>

<p>The final step in the form-handling part of the view is to redirect to another page, usually a "success" page. In this case, we use <code>HttpResponseRedirect</code> and <code>reverse()</code> to redirect to the view named <code>'all-borrowed'</code> (this was created as the "challenge" in <a href="/en-US/docs/Learn/Server-side/Django/Authentication#challenge_yourself">Django Tutorial Part 8: User authentication and permissions</a>). If you didn't create that page consider redirecting to the home page at URL '/').</p>

<p>That's everything needed for the form handling itself, but we still need to restrict access to the view to just logged-in librarians who have permission to renew books. We use <code>@login_required</code> to require that the user is logged in, and the <code>@permission_required</code> function decorator with our existing <code>can_mark_returned</code> permission to allow access (decorators are processed in order). Note that we probably should have created a new permission setting in <code>BookInstance</code> ("<code>can_renew</code>"), but we will reuse the existing one to keep the example simple. </p>

<p>The final view is therefore as shown below. Please copy this into the bottom of <strong>locallibrary/catalog/views.py</strong>.</p>

<pre class="brush: python">import datetime

from django.contrib.auth.decorators import login_required, permission_required
from django.shortcuts import get_object_or_404
from django.http import HttpResponseRedirect
from django.urls import reverse

from catalog.forms import RenewBookForm

@login_required
@permission_required('catalog.can_mark_returned', raise_exception=True)
def renew_book_librarian(request, pk):
    """View function for renewing a specific BookInstance by librarian."""
    book_instance = get_object_or_404(BookInstance, pk=pk)

    # If this is a POST request then process the Form data
    if request.method == 'POST':

        # Create a form instance and populate it with data from the request (binding):
        form = RenewBookForm(request.POST)

        # Check if the form is valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required (here we just write it to the model due_back field)
            book_instance.due_back = form.cleaned_data['renewal_date']
            book_instance.save()

            # redirect to a new URL:
            return HttpResponseRedirect(reverse('all-borrowed') )

    # If this is a GET (or any other method) create the default form.
    else:
        proposed_renewal_date = datetime.date.today() + datetime.timedelta(weeks=3)
        form = RenewBookForm(initial={'renewal_date': proposed_renewal_date})

    context = {
        'form': form,
        'book_instance': book_instance,
    }

    return render(request, 'catalog/book_renew_librarian.html', context)
</pre>

<h3 id="The_template">The template</h3>

<p>Create the template referenced in the view (<strong>/catalog/templates/catalog/book_renew_librarian.html</strong>) and copy the code below into it:</p>

<pre class="brush: html">{% extends "base_generic.html" %}

{% block content %}
  &lt;h1&gt;Renew: \{{ book_instance.book.title }}&lt;/h1&gt;
  &lt;p&gt;Borrower: \{{ book_instance.borrower }}&lt;/p&gt;
  &lt;p{% if book_instance.is_overdue %} class="text-danger"{% endif %}&gt;Due date: \{{ book_instance.due_back }}&lt;/p&gt;

  &lt;form action="" method="post"&gt;
    {% csrf_token %}
    &lt;table&gt;
    \{{ form.as_table }}
    &lt;/table&gt;
    &lt;input type="submit" value="Submit"&gt;
  &lt;/form&gt;
{% endblock %}</pre>

<p>Most of this will be completely familiar from previous tutorials.
We extend the base template and then redefine the content block. We are able to reference <code>\{{ book_instance }}</code> (and its variables) because it was passed into the context object in the <code>render()</code> function, and we use these to list the book title, borrower, and the original due date.</p>

<p>The form code is relatively simple. First, we declare the <code>form</code> tags, specifying where the form is to be submitted (<code>action</code>) and the <code>method</code> for submitting the data (in this case an "HTTP POST") — if you recall the <a href="#html_forms">HTML Forms</a> overview at the top of the page, an empty <code>action</code> as shown, means that the form data will be posted back to the current URL of the page (which is what we want!). Inside the tags, we define the <code>submit</code> input, which a user can press to submit the data. The <code>{% csrf_token %}</code> added just inside the form tags is part of Django's cross-site forgery protection.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Add the <code>{% csrf_token %}</code> to every Django template you create that uses <code>POST</code> to submit data. This will reduce the chance of forms being hijacked by malicious users.</p>
</div>

<p>All that's left is the <code>\{{ form }}</code> template variable, which we passed to the template in the context dictionary. Perhaps unsurprisingly, when used as shown this provides the default rendering of all the form fields, including their labels, widgets, and help text — the rendering is as shown below:</p>

<pre class="brush: html">&lt;tr&gt;
  &lt;th&gt;&lt;label for="id_renewal_date"&gt;Renewal date:&lt;/label&gt;&lt;/th&gt;
  &lt;td&gt;
    &lt;input id="id_renewal_date" name="renewal_date" type="text" value="2016-11-08" required&gt;
    &lt;br&gt;
    &lt;span class="helptext"&gt;Enter date between now and 4 weeks (default 3 weeks).&lt;/span&gt;
  &lt;/td&gt;
&lt;/tr&gt;
</pre>

<div class="notecard note">
  <p><strong>Note:</strong> It is perhaps not obvious because we only have one field, but, by default, every field is defined in its own table row. This same rendering is provided if you reference the template variable <code>\{{ form.as_table }}</code>.</p>
</div>

<p>If you were to enter an invalid date, you'd additionally get a list of the errors rendered on the page (see <code>errorlist</code> below).</p>

<pre class="brush: html">&lt;tr&gt;
  &lt;th&gt;&lt;label for="id_renewal_date"&gt;Renewal date:&lt;/label&gt;&lt;/th&gt;
    &lt;td&gt;
      &lt;ul class="errorlist"&gt;
        &lt;li&gt;Invalid date - renewal in past&lt;/li&gt;
      &lt;/ul&gt;
      &lt;input id="id_renewal_date" name="renewal_date" type="text" value="2015-11-08" required&gt;
      &lt;br&gt;
      &lt;span class="helptext"&gt;Enter date between now and 4 weeks (default 3 weeks).&lt;/span&gt;
    &lt;/td&gt;
&lt;/tr&gt;</pre>

<h4 id="Other_ways_of_using_form_template_variable">Other ways of using form template variable</h4>

<p>Using <code>\{{ form.as_table }}</code> as shown above, each field is rendered as a table row. You can also render each field as a list item (using <code>\{{ form.as_ul }}</code> ) or as a paragraph (using <code>\{{ form.as_p }}</code>).</p>

<p>It is also possible to have complete control over the rendering of each part of the form, by indexing its properties using dot notation. So, for example, we can access a number of separate items for our <code>renewal_date</code> field:</p>

<ul>
 <li><code>\{{ form.renewal_date }}:</code> The whole field.</li>
 <li><code>\{{ form.renewal_date.errors }}</code>: The list of errors.</li>
 <li><code>\{{ form.renewal_date.id_for_label }}</code>: The id of the label.</li>
 <li><code>\{{ form.renewal_date.help_text }}</code>: The field help text.</li>
</ul>

<p>For more examples of how to manually render forms in templates and dynamically loop over template fields, see <a href="https://docs.djangoproject.com/en/3.1/topics/forms/#rendering-fields-manually">Working with forms &gt; Rendering fields manually</a> (Django docs).</p>

<h3 id="Testing_the_page">Testing the page</h3>

<p>If you accepted the "challenge" in <a href="/en-US/docs/Learn/Server-side/Django/Authentication#challenge_yourself">Django Tutorial Part 8: User authentication and permissions</a> you'll have a list of all books on loan in the library, which is only visible to library staff. We can add a link to our renew page next to each item using the template code below.</p>

<pre class="brush: html">{% if perms.catalog.can_mark_returned %}- &lt;a href="{% url 'renew-book-librarian' bookinst.id %}"&gt;Renew&lt;/a&gt;  {% endif %}</pre>

<div class="notecard note">
<p><strong>Note:</strong> Remember that your test login will need to have the permission "<code>catalog.can_mark_returned</code>" in order to access the renew book page (perhaps use your superuser account).</p>
</div>

<p>You can alternatively manually construct a test URL like this — <code>http://127.0.0.1:8000/catalog/book/<em>&lt;bookinstance_id&gt;</em>/renew/</code> (a valid <code>bookinstance_id</code> can be obtained by navigating to a book detail page in your library, and copying the <code>id</code> field).</p>

<h3 id="What_does_it_look_like">What does it look like?</h3>

<p>If you are successful, the default form will look like this:</p>

<p><img alt="" src="forms_example_renew_default.png"></p>

<p>The form with an invalid value entered will look like this:</p>

<p><img alt="" src="forms_example_renew_invalid.png"></p>

<p>The list of all books with renew links will look like this:</p>

<p><img alt="" src="forms_example_renew_allbooks.png"></p>

<h2 id="ModelForms">ModelForms</h2>

<p>Creating a <code>Form</code> class using the approach described above is very flexible, allowing you to create whatever sort of form page you like and associate it with any model or models.</p>

<p>However, if you just need a form to map the fields of a <em>single</em> model then your model will already define most of the information that you need in your form: fields, labels, help text and so on. Rather than recreating the model definitions in your form, it is easier to use the <a href="https://docs.djangoproject.com/en/3.1/topics/forms/modelforms/">ModelForm</a> helper class to create the form from your model. This <code>ModelForm</code> can then be used within your views in exactly the same way as an ordinary <code>Form</code>.</p>

<p>A basic <code>ModelForm</code> containing the same field as our original <code>RenewBookForm</code> is shown below. All you need to do to create the form is add <code>class Meta</code> with the associated <code>model</code> (<code>BookInstance</code>) and a list of the model <code>fields</code> to include in the form.</p>

<pre class="brush: python">from django.forms import ModelForm

from catalog.models import BookInstance

class RenewBookModelForm(ModelForm):
    class Meta:
        model = BookInstance
        fields = ['due_back']
</pre>

<div class="notecard note">
  <p><strong>Note:</strong> You can also include all fields in the form using <code>fields = '__all__'</code>, or you can use <code>exclude</code> (instead of <code>fields</code>) to specify the fields <em>not</em> to include from the model).</p>

<p>Neither approach is recommended because new fields added to the model are then automatically included in the form (without the developer necessarily considering possible security implications).</p>
</div>

<div class="notecard note">
  <p><strong>Note:</strong> This might not look like all that much simpler than just using a <code>Form</code> (and it isn't in this case, because we just have one field). However, if you have a lot of fields, it can reduce the amount of code quite significantly!</p>
</div>

<p>The rest of the information comes from the model field definitions (e.g. labels, widgets, help text, error messages). If these aren't quite right, then we can override them in our <code>class Meta</code>, specifying a dictionary containing the field to change and its new value. For example, in this form, we might want a label for our field of "<em>Renewal date</em>" (rather than the default based on the field name: <em>Due Back</em>), and we also want our help text to be specific to this use case.
The <code>Meta</code> below shows you how to override these fields, and you can similarly set <code>widgets</code> and <code>error_messages</code> if the defaults aren't sufficient.</p>

<pre class="brush: python">class Meta:
    model = BookInstance
    fields = ['due_back']
    labels = {'due_back': _('New renewal date')}
    help_texts = {'due_back': _('Enter a date between now and 4 weeks (default 3).')} 
</pre>

<p>To add validation you can use the same approach as for a normal <code>Form</code> — you define a function named <code>clean_<em>field_name</em>()</code> and raise <code>ValidationError</code> exceptions for invalid values.
The only difference with respect to our original form is that the model field is named <code>due_back</code> and not "<code>renewal_date</code>".
This change is necessary since the corresponding field in <code>BookInstance</code> is called <code>due_back</code>. </p>

<pre class="brush: python">from django.forms import ModelForm

from catalog.models import BookInstance

class RenewBookModelForm(ModelForm):
    def clean_due_back(self):
       data = self.cleaned_data['due_back']

       # Check if a date is not in the past.
       if data &lt; datetime.date.today():
           raise ValidationError(_('Invalid date - renewal in past'))

       # Check if a date is in the allowed range (+4 weeks from today).
       if data &gt; datetime.date.today() + datetime.timedelta(weeks=4):
           raise ValidationError(_('Invalid date - renewal more than 4 weeks ahead'))

       # Remember to always return the cleaned data.
       return data

    class Meta:
        model = BookInstance
        fields = ['due_back']
        labels = {'due_back': _('Renewal date')}
        help_texts = {'due_back': _('Enter a date between now and 4 weeks (default 3).')}
</pre>

<p>The class <code>RenewBookModelForm</code> above is now functionally equivalent to our original <code>RenewBookForm</code>. You could import and use it wherever you currently use <code>RenewBookForm</code> as long as you also update the corresponding form variable name from <code>renewal_date</code> to <code>due_back</code> as in the second form declaration: <code>RenewBookModelForm(initial={'due_back': proposed_renewal_date}</code>.</p>

<h2 id="Generic_editing_views">Generic editing views</h2>

<p>The form handling algorithm we used in our function view example above represents an extremely common pattern in form editing views. Django abstracts much of this "boilerplate" for you, by creating <a href="https://docs.djangoproject.com/en/3.1/ref/class-based-views/generic-editing/">generic editing views</a> for creating, editing, and deleting views based on models. Not only do these handle the "view" behavior, but they automatically create the form class (a <code>ModelForm</code>) for you from the model.</p>

<div class="notecard note">
  <p><strong>Note:</strong> In addition to the editing views described here, there is also a <a href="https://docs.djangoproject.com/en/3.1/ref/class-based-views/generic-editing/#formview">FormView</a> class, which lies somewhere between our function view and the other generic views in terms of "flexibility" vs "coding effort". Using <code>FormView</code>, you still need to create your <code>Form</code>, but you don't have to implement all of the standard form-handling patterns. Instead, you just have to provide an implementation of the function that will be called once the submission is known to be valid.</p>
</div>

<p>In this section, we're going to use generic editing views to create pages to add functionality to create, edit, and delete <code>Author</code> records from our library — effectively providing a basic reimplementation of parts of the Admin site (this could be useful if you need to offer admin functionality in a more flexible way than can be provided by the admin site).</p>

<h3 id="Views">Views</h3>

<p>Open the views file (<strong>locallibrary/catalog/views.py</strong>) and append the following code block to the bottom of it:</p>

<pre class="brush: python">from django.views.generic.edit import CreateView, UpdateView, DeleteView
from django.urls import reverse_lazy

from catalog.models import Author

class AuthorCreate(CreateView):
    model = Author
    fields = ['first_name', 'last_name', 'date_of_birth', 'date_of_death']
    initial = {'date_of_death': '11/06/2020'}

class AuthorUpdate(UpdateView):
    model = Author
    fields = '__all__' # Not recommended (potential security issue if more fields added)

class AuthorDelete(DeleteView):
    model = Author
    success_url = reverse_lazy('authors')</pre>

<p>As you can see, to create, update, or delete the views you need to derive from <code>CreateView</code>, <code>UpdateView</code>, and <code>DeleteView</code> (respectively) and then define the associated model.</p>

<p>For the "create" and "update" cases you also need to specify the fields to display in the form (using the same syntax as for <code>ModelForm</code>). In this case, we show how to list them individually and the syntax to list "all" fields. You can also specify initial values for each of the fields using a dictionary of <em>field_name</em>/<em>value</em> pairs (here we arbitrarily set the date of death for demonstration purposes — you might want to remove that!). By default, these views will redirect on success to a page displaying the newly created/edited model item, which in our case will be the author detail view we created in a previous tutorial. You can specify an alternative redirect location by explicitly declaring parameter <code>success_url</code> (as done for the <code>AuthorDelete</code> class).</p>

<p>The <code>AuthorDelete</code> class doesn't need to display any of the fields, so these don't need to be specified. You do however need to specify the <code>success_url</code>, because there is no obvious default value for Django to use. In this case, we use the <code><a href="https://docs.djangoproject.com/en/3.1/ref/urlresolvers/#reverse-lazy">reverse_lazy()</a></code> function to redirect to our author list after an author has been deleted — <code>reverse_lazy()</code> is a lazily executed version of <code>reverse()</code>, used here because we're providing a URL to a class-based view attribute.</p>

<h3 id="Templates">Templates</h3>

<p>The "create" and "update" views use the same template by default, which will be named after your model: <em>model_name</em><strong>_form.html</strong> (you can change the suffix to something other than <strong>_form</strong> using the <code>template_name_suffix</code> field in your view, for example <code>template_name_suffix = '_other_suffix'</code>)</p>

<p>Create the template file <strong>locallibrary/catalog/templates/catalog/author_form.html</strong> and copy in the text below.</p>

<pre class="brush: html">{% extends "base_generic.html" %}

{% block content %}
  &lt;form action="" method="post"&gt;
    {% csrf_token %}
    &lt;table&gt;
    \{{ form.as_table }}
    &lt;/table&gt;
    &lt;input type="submit" value="Submit"&gt;
  &lt;/form&gt;
{% endblock %}</pre>

<p>This is similar to our previous forms and renders the fields using a table. Note also how again we declare the <code>{% csrf_token %}</code> to ensure that our forms are resistant to CSRF attacks.</p>

<p>The "delete" view expects to find a template named with the format <em>model_name</em><strong>_confirm_delete.html</strong> (again, you can change the suffix using <code>template_name_suffix</code> in your view). Create the template file <strong>locallibrary/catalog/templates/catalog/author_confirm_delete.html</strong> and copy in the text below.</p>

<pre class="brush: html">{% extends "base_generic.html" %}

{% block content %}

&lt;h1&gt;Delete Author&lt;/h1&gt;

&lt;p&gt;Are you sure you want to delete the author: \{{ author }}?&lt;/p&gt;

&lt;form action="" method="POST"&gt;
  {% csrf_token %}
  &lt;input type="submit" value="Yes, delete."&gt;
&lt;/form&gt;

{% endblock %}
</pre>

<h3 id="URL_configurations">URL configurations</h3>

<p>Open your URL configuration file (<strong>locallibrary/catalog/urls.py</strong>) and add the following configuration to the bottom of the file:</p>

<pre class="brush: python">urlpatterns += [
    path('author/create/', views.AuthorCreate.as_view(), name='author-create'),
    path('author/&lt;int:pk&gt;/update/', views.AuthorUpdate.as_view(), name='author-update'),
    path('author/&lt;int:pk&gt;/delete/', views.AuthorDelete.as_view(), name='author-delete'),
]</pre>

<p>There is nothing particularly new here! You can see that the views are classes, and must hence be called via <code>.as_view()</code>, and you should be able to recognize the URL patterns in each case. We must use <code>pk</code> as the name for our captured primary key value, as this is the parameter name expected by the view classes.</p>

<p>The author create, update, and delete pages are now ready to test (we won't bother hooking them into the site sidebar in this case, although you can do so if you wish).</p>

<div class="notecard note">
    <p><strong>Note:</strong> Observant users will have noticed that we didn't do anything to prevent unauthorized users from accessing the pages! We leave that as an exercise for you (hint: you could use the <code>PermissionRequiredMixin</code> and either create a new permission or reuse our <code>can_mark_returned</code> permission).</p>
</div>

<h3 id="Testing_the_page_2">Testing the page</h3>

<p>First, log in to the site with an account that has whatever permissions you decided are needed to access the author editing pages.</p>

<p>Then navigate to the author create page: <a href="http://127.0.0.1:8000/catalog/author/create/">http://127.0.0.1:8000/catalog/author/create/</a>, which should look like the screenshot below.</p>

<p><img alt="Form Example: Create Author" src="forms_example_create_author.png"></p>

<p>Enter values for the fields and then press <strong>Submit</strong> to save the author record. You should now be taken to a detail view for your new author, with a URL of something like <em>http://127.0.0.1:8000/catalog/author/10</em>.</p>

<p>You can test editing records by appending <em>/update/</em> to the end of the detail view URL (e.g. <em>http://127.0.0.1:8000/catalog/author/10/update/</em>) — we don't show a screenshot, because it looks just like the "create" page!</p>

<p>Finally, we can delete the page by appending delete to the end of the author detail-view URL (e.g. <em>http://127.0.0.1:8000/catalog/author/10/delete/</em>). Django should display the delete page shown below. Press "<strong>Yes, delete.</strong>" to remove the record and be taken to the list of all authors.</p>

<p><img alt="" src="forms_example_delete_author.png"></p>

<h2 id="Challenge_yourself">Challenge yourself</h2>

<p>Create some forms to create, edit, and delete <code>Book</code> records. You can use exactly the same structure as for <code>Authors</code>. If your <strong>book_form.html</strong> template is just a copy-renamed version of the <strong>author_form.html</strong> template, then the new "create book" page will look like the screenshot below:</p>

<p><img alt="" src="forms_example_create_book.png"></p>

<h2 id="Summary">Summary</h2>

<p>Creating and handling forms can be a complicated process! Django makes it much easier by providing programmatic mechanisms to declare, render, and validate forms. Furthermore, Django provides generic form editing views that can do <em>almost all</em> the work to define pages that can create, edit, and delete records associated with a single model instance.</p>

<p>There is a lot more that can be done with forms (check out our <a href="#see_also">See also</a> list below), but you should now understand how to add basic forms and form-handling code to your own websites.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/forms/">Working with forms</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/intro/tutorial04/#write-a-simple-form">Writing your first Django app, part 4 &gt; Writing a simple form</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/api/">The Forms API</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/fields/">Form fields</a> (Django docs) </li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/forms/validation/">Form and field validation</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/class-based-views/generic-editing/">Form handling with class-based views</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/topics/forms/modelforms/">Creating forms from models</a> (Django docs)</li>
 <li><a href="https://docs.djangoproject.com/en/3.1/ref/class-based-views/generic-editing/">Generic editing views</a> (Django docs)</li>
</ul>

<p>{{PreviousMenuNext("Learn/Server-side/Django/authentication_and_sessions", "Learn/Server-side/Django/Testing", "Learn/Server-side/Django")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Introduction">Django introduction</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/development_environment">Setting up a Django development environment</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website">Django Tutorial: The Local Library website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/skeleton_website">Django Tutorial Part 2: Creating a skeleton website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Models">Django Tutorial Part 3: Using models</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Admin_site">Django Tutorial Part 4: Django admin site</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Home_page">Django Tutorial Part 5: Creating our home page</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Generic_views">Django Tutorial Part 6: Generic list and detail views</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Sessions">Django Tutorial Part 7: Sessions framework</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Authentication">Django Tutorial Part 8: User authentication and permissions</a></li>
 <li><strong>Django Tutorial Part 9: Working with forms</strong></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Testing">Django Tutorial Part 10: Testing a Django web application</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/Deployment">Django Tutorial Part 11: Deploying Django to production</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/web_application_security">Django web application security</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Django/django_assessment_blog">DIY Django mini blog</a></li>
</ul>