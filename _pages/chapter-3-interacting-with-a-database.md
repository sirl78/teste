---
ID: 40
post_title: 'Chapter 3: Interacting with a Database'
author: Jorge Vieira
post_date: 2016-08-11 13:18:26
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/tutorials/chapter-3-interacting-with-a-database/
published: true
sidebar:
  - ""
footer:
  - ""
header_title_bar:
  - ""
header_transparency:
  - ""
---
[av_section min_height='' min_height_px='500px' padding='large' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='http://aqueduct.stablekernel.com/wp-content/uploads/sites/3/2016/08/slider-about-aqueduct.jpg' attachment='32' attachment_size='full' attach='scroll' position='top center' repeat='stretch' video='' video_ratio='16:9' overlay_enable='aviaTBoverlay_enable' overlay_opacity='0.7' overlay_color='#545355' overlay_pattern='' overlay_custom_pattern='']
[av_heading heading='Interacting with a Database' tag='h1' style='blockquote modern-quote modern-centered' size='' subheading_active='subheading_above' subheading_size='22' padding='10' color='custom-color-heading' custom_font='#ffffff']
Chapter 3
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='large' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']

[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_sidebar widget_area='Tutorial 3']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_textblock size='' font_color='' color='']
This chapter expands on the <a href="/tutorials/chapter-2-writing-tests/">previous</a>.

Now that you’ve seen how to route HTTP requests and respond to them, we’ll do something useful with those requests: interact with a database. We will continue to build on the last chapter project, <code class="highlighter-rouge">quiz</code>, by storing the questions in a database and retrieving them from the database.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="building-models">3.1 Building Models</span>' color='' style='blockquote modern-quote' custom_font='' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
<code class="highlighter-rouge">aqueduct</code> has a built-in ORM (some of which is modeled after the iOS/macOS Core Data framework). Like all ORMs, rows of a database are mapped to objects. In <code class="highlighter-rouge">aqueduct</code>, these instances are of type <code class="highlighter-rouge">Model</code>. Let’s define a model that represents a ‘question’. Create a new directory in <code class="highlighter-rouge">lib</code> named <code class="highlighter-rouge">model</code>, and then add a new file to it named <code class="highlighter-rouge">question.dart</code>.

A model is made up of two classes, its instance type and its persistent type. In your request handling methods, you deal with the instance type. The persistent type is declared once and used to indicate which properties of the model are actually stored in the database. By convention, but not required, persistent types are the name of the instance type, prefixed with ‘_’. In <code class="highlighter-rouge">question.dart</code>, let’s define a persistent type for a question:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">part of quiz;
class _Question {
@primaryKey
int index;

String description;
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, link this file to the rest of your project in <code class="highlighter-rouge">lib/quiz.dart</code> by adding this part at the end of the file: <code class="highlighter-rouge"> part 'model/question.dart'; </code>

A persistent type is a simple Dart class. Each property maps to a column in a database and may be marked with metadata. The <code class="highlighter-rouge">@primaryKey</code> metadata is shorthand for the following metadata:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">@Attributes(primaryKey: true, databaseType: PropertyType.bigInteger, autoincrement: true)</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
<em>All models must have a primary key.</em> Other interesting flags are <code class="highlighter-rouge">indexed</code>, <code class="highlighter-rouge">nullable</code> and <code class="highlighter-rouge">defaultValue</code>. If a property does not have an <code class="highlighter-rouge">Attribute</code>, it is still a persistent property, it’s just a normal column. Supported Dart types are <code class="highlighter-rouge">int</code>, <code class="highlighter-rouge">double</code>, <code class="highlighter-rouge">String</code>, <code class="highlighter-rouge">DateTime</code> and <code class="highlighter-rouge">bool</code>.

Once a persistent type has been defined, you must define an instance type. At the top of this file, but underneath the part of directive, add the following:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">part of quiz;

class Question extends Model implements _Question {}

class _Question {
  @primaryKey
  int index;

  String description;
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Why there is a persistent and instance type we’ll dig into later, but it is important. We now have a model named <code class="highlighter-rouge">Question</code>. We need to set up the application to interact with a database.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="defining-a-context">3.2 Defining a Context</span>' color='' style='blockquote modern-quote' custom_font='' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
In order for an application to work with a database, it needs a <code class="highlighter-rouge">ModelContext</code>. A <code class="highlighter-rouge">ModelContext</code> is the facilitator between your code, model objects and a database. It is made up of two components, a <code class="highlighter-rouge">DataModel</code> (the thing that keeps track of all model types) and <code class="highlighter-rouge">PersistentStore</code> (the thing that talks to the database). These objects are set up in a pipeline. In <code class="highlighter-rouge">pipeline.dart</code>, add the following code to the constructor for <code class="highlighter-rouge">QuizPipeline</code> and define a new property:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class QuizPipeline extends ApplicationPipeline {
  QuizPipeline(Map options) : super(options) {
    var dataModel = new DataModel([Question]);
    var persistentStore = new PostgreSQLPersistentStore.fromConnectionInfo("dart", "dart", "localhost", 5432, "dart_test");
    context = new ModelContext(dataModel, persistentStore);
  }
ModelContext context;

...
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
(In the future, we’ll allow this information to be passed from a configuration and make Dart do the work for us. But for now, we’ll do it manually.)

A <code class="highlighter-rouge">DataModel</code> is initialized with a list of all <em>instance</em> model types. The persistent store is a specific implementation of a persistent store, <code class="highlighter-rouge">PostgreSQLPersistentStore</code> - so by the way, we’re using PostgreSQL. It is initialized with information necessary to connect to a database. (We’ll handle that in a moment.) A <code class="highlighter-rouge">ModelContext</code> simply contains those two things to tie them together.

(By the way, the interface for <code class="highlighter-rouge">PersistentStore</code> can be implemented for different flavors of SQL and even non-SQL databases. We just so happen to prefer PostgreSQL, so we’ve already built that one.)

When a context is instantiated in a pipeline, the static property <code class="highlighter-rouge">ModelContext.defaultContext</code> is set to that instance. When we execute database queries - coming up here soon - they default to this default context. If we have multiple databases, we can create more <code class="highlighter-rouge">ModelContext</code>s and pass them around to make sure we hit the right database. For now, we can ignore this, just know that it exists.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="executing-queries">3.3 Executing Queries</span>' color='' style='blockquote modern-quote' custom_font='' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Now that we have a context - which can establish a connection to a database, talk to the database and map rows to model objects - we can execute queries in our <code class="highlighter-rouge">RequestHandler</code>s. In <code class="highlighter-rouge">question_controller.dart</code>, remove the list of static questions and replace the code for <code class="highlighter-rouge">getAllQuestions</code> (we’ll work on <code class="highlighter-rouge">getQuestionAtIndex</code> soon):
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class QuestionController extends HttpController {
  @httpGet getAllQuestions() async {
    var questionQuery = new Query();
    var questions = await questionQuery.fetch();
    return new Response.ok(questions);
  }

...</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
When this handler method is hit, it’ll create a new <code class="highlighter-rouge">Query</code> for <code class="highlighter-rouge">Question</code> (as indicated by the type parameter). A query has a handful of execution methods on it - <code class="highlighter-rouge">fetch</code>, <code class="highlighter-rouge">fetchOne</code>, <code class="highlighter-rouge">insert</code>, <code class="highlighter-rouge">update</code>, <code class="highlighter-rouge">updateOne</code> and <code class="highlighter-rouge">delete</code>. By executing a <code class="highlighter-rouge">fetch</code> on a vanilla <code class="highlighter-rouge">Query</code>, this will return a list of <code class="highlighter-rouge">Question</code>s, one for each row in the database’s question table. Since <code class="highlighter-rouge">Model</code> objects - which is what <code class="highlighter-rouge">Question</code>s are - implement the <code class="highlighter-rouge">Serializable</code> protocol, we can drop them in as the response body object in <code class="highlighter-rouge">Response</code> and they will be encoded to JSON.

The only problem? We don’t have a database yet.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="configuring-a-database">3.4 Configuring a Database</span>' color='' style='blockquote modern-quote' custom_font='' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
As we’ve mentioned a few times, a key facet to <code class="highlighter-rouge">aqueduct</code> is efficient automated testing. The scheme for testing is to create a ‘test’ database that all of your <code class="highlighter-rouge">aqueduct</code> projects run against. When you run tests against that database, the tests create <em>temporary</em> tables prior to executing. The good news is that the <code class="highlighter-rouge">DataModel</code> in your application can drive this table creation, so you don’t need to do anything special. The tests are run against the current version of the schema, as defined by your code.

Therefore, on any machine you’re going to test on, you need a database (so, your local machine and your CI) that has been configured to have an <code class="highlighter-rouge">aqueduct</code> development database with a specific user. (So, you’ll only need to set this up once.) On macOS, the best way to do this locally is download <a href="http://postgresapp.com">Postgres.app</a>. This has a self-contained instance of Postgres that you start by opening up the application itself. Download this application and run it.

Once it starts running, its icon (an elephant) will appear in your menu bar. Select ‘Open psql’ from its menu, and a command line prompt will appear, connected to the local database. To set up PostgreSQL once and for all for testing, run the following commands in psql:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">create database dart_test;
create user dart with createdb;
alter user dart with password 'dart';
grant all on database dart_test to dart;</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
OK, great, you’re done. (You’ll want to add Postgres.app to your Startup Items or at least remember to open it before you start development work. It only runs on localhost, so it doesn’t open up connections to the outside world.)

You’ll notice in your pipeline, the configuration parameters for the <code class="highlighter-rouge">PostgreSQLPersistentStore</code> match those that you have just added to your local instance of Postgres, so your application will run against that instance. However, if you were to run your code now, the table backing <code class="highlighter-rouge">Question</code>s would not exist. If we are running tests, we need to create a temporary table for <code class="highlighter-rouge">Question</code>s before the tests start. Go to the <code class="highlighter-rouge">setUpAll</code> method in <code class="highlighter-rouge">question_controller_test.dart</code>, and enter the following code after the application is started:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">setUpAll(() async {
  await app.start(runOnMainIsolate: true);
var generator = new SchemaGenerator(ModelContext.defaultContext.dataModel);
var json = generator.serialized;
var pGenerator = new PostgreSQLSchemaGenerator(json, temporary: true);

for (var cmd in pGenerator.commands) {
await ModelContext.defaultContext.persistentStore.execute(cmd);
}
});</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
After the application is started, we know that it creates and sets the <code class="highlighter-rouge">ModelContext.defaultContext</code> with a <code class="highlighter-rouge">DataModel</code> containing <code class="highlighter-rouge">Question</code>. The class <code class="highlighter-rouge">SchemaGenerator</code> will create a database-agnostic JSON schema file. Subclasses of <code class="highlighter-rouge">SchemaGeneratorBackend</code>, like <code class="highlighter-rouge">PostgreSQLSchemaGenerator</code>, can take that JSON and create SQL commands that create all of the tables, indices and constraints defined by the JSON schema. Each of those is executed on the context, creating the schema in the database. (Note that the <code class="highlighter-rouge">temporary</code> flag adds makes all of the tables temporary and therefore they disappear when the database connection in the context’s persistent store closes.)

Because the <code class="highlighter-rouge">PostgreSQLPersistentStore</code>’s connection to the database is also a stream, it, too, must be closed to let the test’s main function terminate. In <code class="highlighter-rouge">tearDownAll</code>, add this code before the app is terminated:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">tearDownAll(() async {
  await ModelContext.defaultContext.persistentStore.close();
  await app.stop();
});</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, if you run your tests, two of them will fail and one of them really should fail, but doesn’t. The first test, that checks that every string returned from the <code class="highlighter-rouge">/questions</code> endpoint should end in a <code class="highlighter-rouge">?</code> succeeds, but that’s because the <code class="highlighter-rouge">everyElement</code> matcher matches every element, and there are no elements, so it doesn’t match any. We never really checked how many questions there were. So, it wasn’t a very good test. We don’t want to specifically say there are just two questions, so we ought to update that test to make sure there is at least one question. We can apply another expectation, just to the body this time. Update that test in <code class="highlighter-rouge">question_controller_test.dart</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">test("/questions returns list of questions", () async {
  var response = await client.request("/questions").get();
  expect(response, hasResponse(200, everyElement(endsWith("?"))));
  expect(response.decodedBody, hasLength(greaterThan(0)));
});&lt;\code&gt;&lt;\pre&gt;
[/av_textblock]

[av_textblock size='' font_color='' color='']
You can access the String body of a <code class="highlighter-rouge">TestResponse</code> directly with <code class="highlighter-rouge">body</code>, its decoded body - like we did here - with <code class="highlighter-rouge">decodedBody</code>. Or, have the analyzer cast the <code class="highlighter-rouge">decodedBody</code> into a <code class="highlighter-rouge">List</code> or <code class="highlighter-rouge">Map</code> with <code class="highlighter-rouge">asList</code> or <code class="highlighter-rouge">asMap</code>. It’s sometimes helpful to print out these values during testing, although using <code class="highlighter-rouge">hasResponse</code> will print out the <code class="highlighter-rouge">TestResponse</code> on failure.

Ok, good, back to all tests failing - as they should, because there are no <code class="highlighter-rouge">Question</code>s in the database and the old <code class="highlighter-rouge">getQuestionAtIndex</code> doesn’t yet use a database query. Let’s first seed the database with some questions using an insert query at the end of <code class="highlighter-rouge">setUpAll</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']</code></pre>
<pre><code class="language-dart">setUpAll(() async {
  await app.start(runOnMainIsolate: true);
  var generator = new SchemaGenerator(ModelContext.defaultContext.dataModel);
  var json = generator.serialized;
  var pGenerator = new PostgreSQLSchemaGenerator(json, temporary: true);
for (var cmd in pGenerator.commands) {
await ModelContext.defaultContext.persistentStore.execute(cmd);
}
var questions = [
"How much wood can a woodchuck chuck?",
"What's the tallest mountain in the world?"
];
for (var question in questions) {
var insertQuery = new Query()
..values.description = question;
await insertQuery.insert();
}
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, this is also a lesson in insert queries. <code class="highlighter-rouge">Query</code> has a property named <code class="highlighter-rouge">values</code>, which will be an instance of the type argument of the <code class="highlighter-rouge">Query</code> - in this case, a <code class="highlighter-rouge">Question</code>. It’s automatically created the first time you access it, so you can simply start setting properties of a <code class="highlighter-rouge">Question</code> on it. When the <code class="highlighter-rouge">Query</code> is inserted, all of the values that have been set on <code class="highlighter-rouge">values</code> property are inserted into the database. (If you don’t set a value, it isn’t sent in the insert query at all. It does not send <code class="highlighter-rouge">null</code> unless you explicitly set a value to <code class="highlighter-rouge">null</code>.)

In our seeded test database, there will be two questions. If you re-run the tests, the first one should pa… wait, no it fails. The test results says this:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">
Expected:
  Status Code: 200
  Body: every element(a string ending with '?')
  Actual: TestResponse:&lt; Status Code: 200 Headers: transfer-encoding: chunked content-encoding: gzip x-frame-options: SAMEORIGIN content-type: application/json; charset=utf-8 x-xss-protection: 1; mode=block x-content-type-options: nosniff server: aqueduct/1 Body: [{"index":1,"description":"How much wood can a woodchuck chuck?"},{"index":2,"description":"What's the tallest mountain in the world?"}]</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
When a <code class="highlighter-rouge">hasResponse</code> matcher fails, it prints out what you expected and what the <code class="highlighter-rouge">TestResponse</code> actually was. The expectation was that every element is a string ending with ‘?’. Instead, the bottom of the test result says that the body is actually a list of maps, for which there is a index and a description. This is the list of JSON-encoded <code class="highlighter-rouge">Question</code> objects, and they are obviously not Strings. Let’s update this test to test each JSON object in the list.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">test("/questions returns list of questions", () async {
  var response = await client.request("/questions").get();
  expect(response, hasResponse(200, everyElement({
      "index" : greaterThan(0),
      "description" : endsWith("?")
  })));
  expect(response.decodedBody, hasLength(greaterThan(0)));
});</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, the expectation is that every element in the response body is a Map, for which it has an <code class="highlighter-rouge">index</code> greater than or equal to 0 and and a <code class="highlighter-rouge">description</code> that ends with <code class="highlighter-rouge">?</code>. Run these tests again, and that first one will now pass. Notice that a <code class="highlighter-rouge">Model</code> object is encoded so that each property is a key in the resulting JSON.

Go ahead and update the second test for a single question to match this same map:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">test("/questions/index returns a single question", () async {
  var response = await client.request("/questions/0").get();
  expect(response, hasResponse(200, {
      "index" : greaterThanOrEqualTo(0),
      "description" : endsWith("?")
  }));
});</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Next, let’s update the <code class="highlighter-rouge">getQuestionAtIndex</code> method to use a query, but apply a predicate (a where clause). We’re going to show you the ugly way first, then a pretty way, then a really pretty way. In <code class="highlighter-rouge">question_controller.dart</code>, replace the <code class="highlighter-rouge">getQuestionAtIndex</code> method.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">@httpGet getQuestionAtIndex(int index) async {
  var questionQuery = new Query()
    ..predicate = new Predicate("index = @idx", {"idx" : index + 1});
  var question = await questionQuery.fetchOne();

  if (question == null) {
    return new Response.notFound();
  }
  return new Response.ok(question);
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
So, this is the ugly way. We set the <code class="highlighter-rouge">predicate</code> of the <code class="highlighter-rouge">Query</code>. A <code class="highlighter-rouge">Predicate</code> is basically the same thing as just writing a where clause, except you don’t pass input values directly to the <code class="highlighter-rouge">Predicate</code> format string, but instead provide a map of key-value pairs, where each key is prefixed with an <code class="highlighter-rouge">@</code> symbol in the format string. (We also increment the index by 1, because we were fetching the question out of a 0-based array, and now we’re fetching it by a 1-based primary key.) We use <code class="highlighter-rouge">fetchOne</code> instead of <code class="highlighter-rouge">fetch</code>, which limits the result set to one row and returns an instance of the type argument of the <code class="highlighter-rouge">Query</code>, <code class="highlighter-rouge">Question</code>, instead of a list. If <code class="highlighter-rouge">fetchOne</code> doesn’t yield any results, it returns <code class="highlighter-rouge">null</code>.

So, this is nice, but the crappy thing about <code class="highlighter-rouge">Predicate</code>s is that they are strings, and refactoring tools generally won’t catch them, and Strings aren’t very safe. We can pretty this up somewhat by using a <code class="highlighter-rouge">ModelQuery</code>. A <code class="highlighter-rouge">ModelQuery</code> allows us to set the predicate by referencing property names and using matchers, similar to test matchers. (Except they are different.) Update that method to use a <code class="highlighter-rouge">ModelQuery</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">@httpGet getQuestionAtIndex(int index) async {
  var questionQuery = new ModelQuery()
    ..["index"] = whereEqualTo(index + 1);    
  var question = await questionQuery.fetchOne();

  if (question == null) {
    return new Response.notFound();
  }
  return new Response.ok(question);
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
To reference a specific property of the Model type argument, you use the brackets and the name of the property. The value is a matcher. All matchers for <code class="highlighter-rouge">Query</code>s begin with the word <code class="highlighter-rouge">where</code>, and there are plenty of them. Check the <code class="highlighter-rouge">aqueduct</code> API reference to see them all. This will yield the same result as the <code class="highlighter-rouge">Predicate</code> from before.

We can still go one step further, and remove all the strings and get the analyzer to make sure we write the correct property names. Back in <code class="highlighter-rouge">model/question.dart</code>, create a subclass of <code class="highlighter-rouge">ModelQuery</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">class QuestionQuery extends ModelQuery implements _Question {}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Note that the type argument is <code class="highlighter-rouge">Question</code> - the instance type - and the implemented interface is <code class="highlighter-rouge">_Question</code>, the persistent type. This will create a new class named <code class="highlighter-rouge">QuestionQuery</code> that will have all the same properties of the persistent type. When we set one of the properties of the persistent type on <code class="highlighter-rouge">QuestionQuery</code>, it automatically looks up the name of that property and invokes the same <code class="highlighter-rouge">["propertyName"]</code> setter as it would in a non-specific <code class="highlighter-rouge">ModelQuery</code>. Update the code again in <code class="highlighter-rouge">getQuestionAtIndex</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">@httpGet getQuestionAtIndex(int index) async {
  var questionQuery = new QuestionQuery()
    ..index = whereEqualTo(index + 1);    
  var question = await questionQuery.fetchOne();

  if (question == null) {
    return new Response.notFound();
  }
  return new Response.ok(question);
}&lt;\code&gt;&lt;\pre&gt;
[/av_textblock]

[av_textblock size='' font_color='' color='']
Run the tests again, good to go!

That’s fetch an insert. Delete works the same way - you specify a predicate, or use a <code class="highlighter-rouge">ModelQuery</code> to define the where clause and invoke <code class="highlighter-rouge">delete</code> on the query. If you want to update database rows, you specify both <code class="highlighter-rouge">values</code> and a predicate or <code class="highlighter-rouge">ModelQuery</code> properties. (Unless you want to update all of the rows, then you don’t specify a predicate.)
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="the-more-you-know">3.5 The more you know: Query parameters</span>' color='' style='blockquote modern-quote' custom_font='' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
You can specify that a <code class="highlighter-rouge">HTTPController</code> handler method extract HTTP query parameters for you and use them in the handler method. We’ll allow the <code class="highlighter-rouge">getAllQuestions</code> method to take a query parameter named <code class="highlighter-rouge">contains</code>. If this query parameter is passed, we’ll filter the questions on whether or not that question contains the value for <code class="highlighter-rouge">contains</code>. In <code class="highlighter-rouge">question_controller.dart</code>, update this method:
[/av_textblock]

[av_textblock size='' font_color='' color='']</code></pre>
<pre><code class="language-dart">@httpGet getAllQuestions({String contains: null}) async {
    var questionQuery = new QuestionQuery();
    if (contains != null) {
      questionQuery.description = whereContains(contains);
    }
    var questions = await questionQuery.fetch();
    return new Response.ok(questions);
  }</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Then, add a new test:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">test("/questions returns list of questions filtered by contains", () async {
  var response = await client.request("/questions?contains=mountain").get();
  expect(response, hasResponse(200, [{
      "index" : greaterThanOrEqualTo(0),
      "description" : "What's the tallest mountain in the world?"
  }]));
  expect(response.decodedBody, hasLength(1));
});</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
This will pass, too - along with the rest of them! Don’t worry, you don’t have to do this - you can grab all query parameters from the <code class="highlighter-rouge">HTTPController</code>’s <code class="highlighter-rouge">request.innerRequest</code>. However, doing this makes your code clearer and will help with the automatic documentation generator we’ll talk about later.
[/av_textblock]

[/av_three_fourth][/av_section]