---
ID: 39
post_title: 'Chapter 2: Writing Tests'
author: Jorge Vieira
post_date: 2016-08-11 13:18:10
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/tutorials/chapter-2-writing-tests/
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
[av_heading heading='Writing Tests' tag='h1' style='blockquote modern-quote modern-centered' size='' subheading_active='subheading_above' subheading_size='22' padding='10' color='custom-color-heading' custom_font='#ffffff']
Chapter 2
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']
[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='']

[av_sidebar widget_area='Tutorial 2']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_textblock size='' font_color='' color='']
This chapter expands on the <a href="/tutorials/chapter-1-routing-and-request-handling/">previous</a>.

One of the core principles of <code class="highlighter-rouge">aqueduct</code> is efficient testing. While opening up your browser and typing in a URL can verify the code you just wrote succeeds, it’s not a very reliable way of testing software. We’ll also run into some dead-ends when we test HTTP requests that use an HTTP method other than GET. Therefore, there are some helpful utilities for writing tests built into <code class="highlighter-rouge">aqueduct</code>.

(As a note, testing Dart in Atom is not well supported - yet. Once you get past this tutorial, it is highly recommended you download IntelliJ IDEA Community Edition for better test support. Most importantly, <code class="highlighter-rouge">aqueduct</code>’s style of testing requires that test files are not run in parallel - and Atom only runs them in parallel. In the meantime, you can install a Terminal plugin for Atom and run the tests correctly using the command <code class="highlighter-rouge">pub run test -j 1</code>.)

In general, testing in Dart is simple: you write a <code class="highlighter-rouge">main</code> function and use the <code class="highlighter-rouge">test</code> function register a test. Each test is a closure that runs some code and has expectations. For example, this code would test that 1 + 1 = 2:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">import 'package:test/test.dart';
void main() {
test("1+1 = 2", () {
expect(1 + 1, equals(2));
});
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Tests are made possible by the <code class="highlighter-rouge">test</code> package which you’ll need to claim as a dependency. In <code class="highlighter-rouge">quiz/pubspec.yaml</code>, add it as a development dependency by adding the following two lines to the end of the file:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">dev_dependencies:
  test: '&gt;=0.12.0 &lt;0.13.0'</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, get the dependencies again by right-clicking on any project file and selecting ‘Pub Get’. (Or run <code class="highlighter-rouge">pub get</code> from the command line in the <code class="highlighter-rouge">quiz</code> directory.)
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="restructuring-quiz">2.1 Restructuring quiz</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Last chapter, we just threw everything in a single file to get started. To test, we really need to add some structure to our project. At the top-level directory <code class="highlighter-rouge">quiz</code>, create a new directory named <code class="highlighter-rouge">lib</code>. In this directory, create a new file named <code class="highlighter-rouge">quiz.dart</code>. This is your library file and it will contain references to every file in your project and packages you wish to import. Add the following:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">library quiz;
import 'package:aqueduct/aqueduct.dart';
export 'package:aqueduct/aqueduct.dart';

part 'controller/question_controller.dart';
part 'pipeline.dart'</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
You’ll get some warnings because <code class="highlighter-rouge">controller/question_controller.dart</code> and <code class="highlighter-rouge">pipeline.dart</code> don’t yet exist. So, let’s create those. Create a new directory at <code class="highlighter-rouge">quiz/lib/controller</code> and add the file <code class="highlighter-rouge">question_controller.dart</code> to that directory. At the top of this file, link this ‘part’ back to the library file and then copy and paste the <code class="highlighter-rouge">QuestionController</code> class from <code class="highlighter-rouge">bin/quiz.dart</code> into it:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">part of quiz;
class QuestionController extends HttpController {
var questions = [
"How much wood can a woodchuck chuck?",
"What's the tallest mountain in the world?"
];

@httpGet getAllQuestions() async {
return new Response.ok(questions);
}

@httpGet getQuestionAtIndex(int index) async {
if (index &lt; 0 || index &gt;= questions.length) {
return new Response.notFound();
}

return new Response.ok(questions[index]);
}
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Next, create a new file at <code class="highlighter-rouge">lib/pipeline.dart</code>, link this part back to the library, and copy and paste the <code class="highlighter-rouge">QuizPipeline</code> class into this file:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">part of quiz;
class QuizPipeline extends ApplicationPipeline {
QuizPipeline(Map options) : super(options);

void addRoutes() {
router
.route("/questions/[:index(\\d+)]")
.next(() =&gt; new QuestionController());
}
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now that you’ve moved the definition of your quiz application to a library, you can update <code class="highlighter-rouge">bin/quiz.dart</code> to import your <code class="highlighter-rouge">quiz</code> library and only have the main function. (Remember, there are two <code class="highlighter-rouge">quiz.dart</code> files now - this isn’t a requirement by any means, you can name the file in <code class="highlighter-rouge">bin</code> whatever you want.)
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">import 'package:quiz/quiz.dart';
void main() {
var app = new Application();

app.start();
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Note that the import statement changed from importing <code class="highlighter-rouge">aqueduct</code> to <code class="highlighter-rouge">quiz</code>. Since the <code class="highlighter-rouge">quiz</code> library exports <code class="highlighter-rouge">aqueduct</code>, any file that imports <code class="highlighter-rouge">quiz</code> will also import <code class="highlighter-rouge">aqueduct</code>. Finally, get your dependencies again to get your project to recognize that <code class="highlighter-rouge">quiz</code> is now a library package. You can ensure that everything still works by running <code class="highlighter-rouge">bin/quiz.dart</code> again and typing the URL into your browser.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="writings-tests">2.2 Writings Tests</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
We’d like to ensure that when we hit the <code class="highlighter-rouge">/questions</code> endpoint, that we actually get questions. What does that mean? Well, that is up to us. But, let’s say that ‘questions’ means ‘a list of strings that all end in a question mark’.

In Dart, tests are stored in a top-level <code class="highlighter-rouge">test</code> directory. Create that directory in <code class="highlighter-rouge">quiz</code>. Then, add a new file to it named <code class="highlighter-rouge">question_controller_test.dart</code>. (Test must end in <code class="highlighter-rouge">_test.dart</code> and live in the <code class="highlighter-rouge">test</code> directory for the tools to find them without you having to specify their path.) In this file, import both the <code class="highlighter-rouge">test</code> and <code class="highlighter-rouge">quiz</code> package.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">import 'package:test/test.dart';
import 'package:quiz/quiz.dart';</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The way <code class="highlighter-rouge">aqueduct</code> accomplishes testing is by starting an entire application, running the tests, then stopping the application. The <code class="highlighter-rouge">Application</code> class is set up to handle this quite nicely, and in later chapters, we’ll see that there some other tools for making that easy as an application continues to grow. A Dart test file can declare a <code class="highlighter-rouge">setUpAll</code> and <code class="highlighter-rouge">tearDownAll</code> method to run before and after all tests. After the import statements, add a <code class="highlighter-rouge">main</code> function with the appropriate setup and teardown code:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">void main() {
  var app = new Application();
setUpAll(() async {
await app.start(runOnMainIsolate: true);
});

tearDownAll(() async {
await app.stop();
});
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Once we add tests and run this test file, an instance of a <code class="highlighter-rouge">QuizPipeline</code> driven <code class="highlighter-rouge">Application</code> will be started. Because starting an application takes a few milliseconds, we must make sure that we <code class="highlighter-rouge">await</code> it startup prior to moving on to the tests. Likewise, we may run multiple groups of tests or files with different tests in them, so we have to shut down the application when the tests are finished to free up the port the <code class="highlighter-rouge">Application</code> is listening on. (You really really shouldn’t forget to shut it down, because if you don’t, subsequent tests will start to fail because the application can’t bind to the listening port.)

Notice also that <code class="highlighter-rouge">start</code> takes an optional argument, <code class="highlighter-rouge">runOnMainIsolate</code>. In the previous chapter, we talked about an application spreading across multiple isolates. All of that behavior is tested in <code class="highlighter-rouge">aqueduct</code>, and so your tests should only test the logic of your pipeline and its related <code class="highlighter-rouge">RequestHandler</code>s. Since isolates can’t share memory, if you ever want to dig into your pipeline and check things out or use some of its resources directly, you wouldn’t be able to do that from the tests - the tests would run on a separate isolate from the pipeline. Therefore, when running tests, you should set this flag to true. (This flag is specifically meant for tests.)

Now, we need to add a test to verify that hitting the <code class="highlighter-rouge">/questions</code> endpoint does return our definition of ‘questions’. In <code class="highlighter-rouge">aqueduct</code>, there is a utility called a <code class="highlighter-rouge">TestClient</code> to make this a lot easier. At the top of your main function, but after we create the application instance, declare a new variable:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">void main() {
  var app = new Application();
  var client = new TestClient(app.configuration.port);
...</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
A <code class="highlighter-rouge">TestClient</code> will execute HTTP requests on your behalf in your tests, and is configured to point at the running application. Testing an <code class="highlighter-rouge">aqueduct</code> application is generally two steps: make a request and then verify you got the response you wanted. Let’s create a new test and do the first step. Near the end of main, add the following test:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">void main() {
  ...
test("/questions returns list of questions", () async {
var response = await client.request("/questions").get();
});
}</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
If you run this test file now, an instance of your application will spin up on the main isolate, and your first test will execute a GET <code class="highlighter-rouge">http://localhost:8080/questions</code>, then your application will be torn down. Of course, we don’t verify anything about the response, so we should actually do something there.

The value of <code class="highlighter-rouge">response</code> in the previous code snippet is an instance of <code class="highlighter-rouge">TestResponse</code>. Dart tests use the Hamcrest style matchers in their expectations. There are built-in matchers in <code class="highlighter-rouge">aqueduct</code> for setting up and matching expectations on <code class="highlighter-rouge">TestResponse</code> instances. For example, if we wanted to verify that we got a 404 back, we’d simply do:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">  expect(response, hasStatus(404));</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
But here, we want to verify that we get back a 200 and that the response body is a list of questions. Add the following code to the end of the test:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">test("/questions returns list of questions", () async {
  var response = await client.request("/questions").get();
  expect(response, hasResponse(200, everyElement(endsWith("?"))));
});</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, make sure you shut down your application if you were running it from a previous chapter. To run a test file in Atom, you can do two things: manually hit Cmd-Shift-P and type in run test or use the keyboard shortcut, Cmd-Option-Ctrl-T. The test results will appear in a panel. (Make sure you save your test file first! Oh, and you can also run the tests just by running the test file in the same way you ran the quiz.dart file. Atom currently isn’t great at displaying test results. A more powerful option is IntelliJ IDEA Community Edition, but Atom is a lot friendlier for a tutorial.)
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="what-sort-of-wizardry">2.3 What sort of wizardry is this?</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
The <code class="highlighter-rouge">hasResponse</code> matcher takes two arguments: a status code and a ‘body matcher’. If the response’s status code is 200, this matcher will move on to matching the body. The body matcher is more of a workhorse. First, within the <code class="highlighter-rouge">TestClient</code>, the body of the HTTP response is decoded according to its <code class="highlighter-rouge">Content-Type</code> header. Then, that decoded body is matched against the body matcher.

There are a LOT of built-in matchers - see the documentation for the test package <a href="https://www.dartdocs.org/documentation/test/0.12.13%2B5/">here</a> - and <code class="highlighter-rouge">everyElement</code> and <code class="highlighter-rouge">endsWith</code> are two examples. Since the body is a JSON list of strings, it’ll be decoded into a Dart list of strings. <code class="highlighter-rouge">everyElement</code> verifies that the decoded body is a list, and then runs the <code class="highlighter-rouge">endsWith</code> matcher on every string in that list. Since every string ends with ?, this matcher as a whole will succeed.

Let’s write two more tests - first, that getting a specific question returns a question (a string with a question mark at the end) and then a test that ensures a question outside of the range of questions will return a 404. Add the following two tests to the bottom of the main function:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">test("/questions/index returns a single question", () async {
  var response = await client.request("/questions/0").get();
  expect(response, hasResponse(200, endsWith("?")));
});
test("/questions/index out of range returns 404", () async {
var response = await client.request("/questions/100").get();
expect(response, hasStatus(404));
})</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Run the tests against, and they should all pass.
[/av_textblock]

[/av_three_fourth]
[/av_section]

[av_section min_height='' min_height_px='500px' padding='large' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']

[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_heading tag='h2' padding='10' heading='What Comes Next?' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[/av_three_fourth][av_one_full first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_hr class='invisible' height='-30' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']

[av_codeblock wrapper_element='' wrapper_element_attributes='']
<!--HubSpot Call-to-Action Code -->
<span id="hs-cta-wrapper-e9a8d607-af72-4aa1-8263-b8919aeb2eeb" class="hs-cta-wrapper">
<span id="hs-cta-e9a8d607-af72-4aa1-8263-b8919aeb2eeb" class="hs-cta-node hs-cta-e9a8d607-af72-4aa1-8263-b8919aeb2eeb">
<!-- [if lte IE 8]>
<div id="hs-cta-ie-element"></div>
<![endif]-->
<a href="http://cta-redirect.hubspot.com/cta/redirect/706489/e9a8d607-af72-4aa1-8263-b8919aeb2eeb"><img id="hs-cta-img-e9a8d607-af72-4aa1-8263-b8919aeb2eeb" class="hs-cta-img" style="border-width: 0px;" src="https://no-cache.hubspot.com/cta/default/706489/e9a8d607-af72-4aa1-8263-b8919aeb2eeb.png" alt="CHAPTER 3: INTERACTING WITH A DATABASE" /></a>
</span>
<script charset="utf-8" src="https://js.hscta.net/cta/current.js"></script>
<script type="text/javascript">
        hbspt.cta.load(706489, 'e9a8d607-af72-4aa1-8263-b8919aeb2eeb', {});
    </script>
</span>
<!-- end HubSpot Call-to-Action Code -->
[/av_codeblock]

[/av_one_full][/av_section][av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' bottom_border_diagonal_color='#333333' bottom_border_diagonal_direction='' bottom_border_style='' id='' color='main_color' custom_bg='#545355' src='http://aqueduct.stablekernel.com/wp-content/uploads/sites/3/2016/08/Aqueduct-News.jpg' attachment='178' attachment_size='full' attach='scroll' position='center center' repeat='stretch' video='' video_ratio='16:9' overlay_enable='aviaTBoverlay_enable' overlay_opacity='0.5' overlay_color='#000000' overlay_pattern='' overlay_custom_pattern='']

[av_one_full first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_heading tag='h2' padding='10' heading='Get Aqueduct News ' color='custom-color-heading' style='blockquote modern-quote modern-centered' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[/av_one_full][av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[/av_one_fourth][av_one_half min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_codeblock wrapper_element='' wrapper_element_attributes='']
<!--HubSpot Call-to-Action Code -->
<span id="hs-cta-wrapper-38cddba6-7fda-475c-9b91-421388cbf122" class="hs-cta-wrapper">
<span id="hs-cta-38cddba6-7fda-475c-9b91-421388cbf122" class="hs-cta-node hs-cta-38cddba6-7fda-475c-9b91-421388cbf122">
<!-- [if lte IE 8]>
<div id="hs-cta-ie-element"></div>
<![endif]-->
<a href="http://cta-redirect.hubspot.com/cta/redirect/706489/38cddba6-7fda-475c-9b91-421388cbf122"><img id="hs-cta-img-38cddba6-7fda-475c-9b91-421388cbf122" class="hs-cta-img" style="border-width: 0px;" src="https://no-cache.hubspot.com/cta/default/706489/38cddba6-7fda-475c-9b91-421388cbf122.png" alt="GET AQUEDUCT NEWS" /></a>
</span>
<script charset="utf-8" src="https://js.hscta.net/cta/current.js"></script>
<script type="text/javascript">
        hbspt.cta.load(706489, '38cddba6-7fda-475c-9b91-421388cbf122', {});
    </script>
</span>
<!-- end HubSpot Call-to-Action Code -->
[/av_codeblock]

[/av_one_half][av_one_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[/av_one_fourth][/av_section]