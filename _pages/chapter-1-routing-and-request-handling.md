---
ID: 38
post_title: 'Chapter 1: Routing and Request Handling'
author: Jorge Vieira
post_date: 2016-08-11 13:17:52
post_excerpt: ""
layout: page
permalink: >
  https://aqueduct.io/tutorials/chapter-1-routing-and-request-handling/
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
[av_heading tag='h1' padding='10' heading='Routing and Request Handling' color='custom-color-heading' style='blockquote modern-quote modern-centered' custom_font='#ffffff' size='' subheading_active='subheading_above' subheading_size='22' custom_class='']
Chapter 1
[/av_heading]

[av_hr class='invisible' height='20' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']
[/av_section]

[av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']

[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='' mobile_display='']

[av_sidebar widget_area='Tutorial 1']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_heading tag='h2' padding='10' heading='<span id="installing-dart">1.1 Installing Dart</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
If you have Homebrew installed, run these commands from terminal:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">brew tap dart-lang/dart
brew install dart
gdggdg
gd
gdfgdgdgd
dg
</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
If you don’t have Homebrew installed or you are on another platform, visit <a href="https://www.dartlang.org/downloads">https://www.dartlang.org/downloads</a>. It’ll be quick, promise.

You should really install Atom as well for editing your code, and the Dart support is great. You can get it from <a href="https://atom.io/" target="_blank">https://atom.io</a>. Once Atom is installed, install the ‘dartlang’ package from ‘dart-atom’ (not any of the other ones).
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="creating-a-project">1.2 Creating a Project</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
To start, we’ll keep it simple. Create a new directory named <code class="highlighter-rouge">quiz</code> (ensure that it is lowercase). Within this directory, create a new file named <code class="highlighter-rouge">pubspec.yaml</code>. Dart uses this file to define your project and its dependencies.

In the pubspec, enter the following markup:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre><code class="language-dart">name: quiz
description: A quiz web server
version: 0.0.1
author: Me

environment:
  sdk: '&gt;=1.0.0 &lt;2.0.0'

dependencies:
  aqueduct: any</code></pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
This pubspec now defines a project named <code class="highlighter-rouge">quiz</code> (all Dart files and project identifiers are snake case), indicates that it uses a version of the Dart SDK between 1.0 and 2.0, and depends on the <code class="highlighter-rouge">aqueduct</code> package. Save this file in the <code class="highlighter-rouge">quiz</code> directory.

Next, you will fetch the dependencies of the <code class="highlighter-rouge">quiz</code> project. If you are using Atom, you’ll get a popup that tells you to do this and you can just click the button. (You may also right-click on file in Atom and select ‘Pub Get’). If you aren’t using Atom, from the command line, run the following from inside the <code class="highlighter-rouge">quiz</code> directory:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-bash" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">pub get</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Dependencies get stored in the directory ~/.pub-cache. Dart creates some project-specific files to reference the dependencies in that global cache in the project directory. You won’t have to worry about that, though, since you’ll never have to deal with it directly. Sometimes, it’s just nice to know where things are. (There is one other file, called <code class="highlighter-rouge">pubspec.lock</code> that you do care about, but we’ll chat about it later.)

With this dependency installed, your project can use <code class="highlighter-rouge">aqueduct</code>. For this simple getting started guide, we won’t structure a full project and just focus on getting an <code class="highlighter-rouge">aqueduct</code> web server up and running. Create a new directory named <code class="highlighter-rouge">bin</code> and add a file to it named <code class="highlighter-rouge">quiz.dart</code>. At the top of this file, import the <code class="highlighter-rouge">aqueduct</code> package:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">import 'package:aqueduct/aqueduct.dart';</pre>
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="handling-requests">1.3 Handling Requests</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
The structure of <code class="highlighter-rouge">aqueduct</code> is like most server-side frameworks: requests go to a router, which then get sent off to a series of request handlers. The most generic request handler is <code class="highlighter-rouge">HTTPController</code>. We’ll create a <code class="highlighter-rouge">HTTPController</code> subclass that will handle requests by returning a list of questions in JSON. In <code class="highlighter-rouge">quiz.dart</code>, create this subclass:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">class QuestionController extends HTTPController {
  var questions = [
    "How much wood can a woodchuck chuck?",
    "What's the tallest mountain in the world?"
  ];

  @httpGet getAllQuestions() async {
    return new Response.ok(questions);
  }
}</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The <code class="highlighter-rouge">QuestionController</code> class defines a property named <code class="highlighter-rouge">questions</code> that has a list of strings. Then, it defines a ‘handler method’ called <code class="highlighter-rouge">getAllQuestions</code>. Handler methods in an <code class="highlighter-rouge">HTTPController</code> are responsible for responding to a request. To be a handler method, it must be marked with the appropriate metadata, in this case, <code class="highlighter-rouge">@httpGet</code>, that defines the HTTP method they handle. There are built-in constants for the common HTTP methods - like <code class="highlighter-rouge">@httpPut</code>, <code class="highlighter-rouge">@httpPost</code> - which are all instances of <code class="highlighter-rouge">HTTPMethod</code>.

To respond, a handler method must return an instance of <code class="highlighter-rouge">Response</code>. A <code class="highlighter-rouge">Response</code> always has a status code. There are built-in convenience constructors for common status codes. In this example, <code class="highlighter-rouge">Response.ok</code> creates a <code class="highlighter-rouge">Response</code> with status code 200. Depending on the convenience constructor used, the argument may mean something different. In the case of <code class="highlighter-rouge">Response.ok</code>, the argument is an object that will be encoded as the HTTP response body.

Therefore, when a HTTP GET gets routed to a <code class="highlighter-rouge">QuestionController</code>, a response with status code 200 and body containing a list of questions will be sent. By default, the response object - here, <code class="highlighter-rouge">questions</code> - gets encoded as JSON before being sent. (We’ll see how that customize that much later.)

Right now, this <code class="highlighter-rouge">QuestionController</code> doesn’t do anything. We need to listen for HTTP requests and feed them to instances of <code class="highlighter-rouge">QuestionController</code> so that it may respond.

<code class="highlighter-rouge">aqueduct</code> takes care of the heavy lifting here. Within the <code class="highlighter-rouge">aqueduct</code> package, there is a class called <code class="highlighter-rouge">Application</code>. An application instance manages starting up an HTTP listener. An application needs an <code class="highlighter-rouge">ApplicationPipeline</code> - a pipeline is the entry point into application-specific code for each request. It defines how those requests get routed and eventually responded to. At the bottom of <code class="highlighter-rouge">quiz.dart</code>, create a <code class="highlighter-rouge">ApplicationPipeline</code> subclass:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">class QuizPipeline extends ApplicationPipeline {
  QuizPipeline(Map options) : super (options);
}</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
A pipeline must have a constructor that takes a <code class="highlighter-rouge">Map</code> of options and forward it on to its superclass’ constructor. In the future, we’ll actually do stuff in this constructor, but for now, we just have to match the signature.

Aside from the constructor, a pipeline must implement the method <code class="highlighter-rouge">addRoutes</code>. Every pipeline has an instance of <code class="highlighter-rouge">Router</code>. A router matches the path of an HTTP request against registered routes to determine where the request goes to be handled. We’ll set up a route so that HTTP requests with the path <code class="highlighter-rouge">/questions</code> get routed to an instance of <code class="highlighter-rouge">QuestionController</code>. Implement <code class="highlighter-rouge">addRoutes</code> in <code class="highlighter-rouge">QuizPipeline</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">class QuizPipeline extends ApplicationPipeline {
  QuizPipeline(Map options) : super (options);

  @override
  void addRoutes() {
    router
      .route("/questions")
      .next(() =&gt; new QuestionController());
  }
}</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Routes are registered with a router using the method <code class="highlighter-rouge">route</code>. If a HTTP request’s path matches that route, the request is forwarded on to the ‘next’ handler. We’ve defined ‘next’ here to be a closure that creates a new instance of <code class="highlighter-rouge">QuestionController</code>. We’ll get to the specifics of all of that in a moment, but we’re almost to the point that we can run this web server, and that seems more exciting.

With a pipeline and a route hooked up, we can write code that starts the web server. At the top of <code class="highlighter-rouge">quiz.dart</code>, underneath the import, define a <code class="highlighter-rouge">main</code> function like so:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">import 'package:aqueduct/aqueduct.dart';

void main() {
  var app = new Application();
  app.start();
}</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
To run the application if you are using Atom, you can right-click on <code class="highlighter-rouge">quiz.dart</code> and select ‘Run Application’. If you wish to run from the command line, run the following from the <code class="highlighter-rouge">quiz</code> directory:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-bash" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">pub run quiz</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
In a browser, open the URL <code class="highlighter-rouge">http://localhost:8080/questions</code>. You’ll see the list of questions! (You can shut down the server by hitting Ctrl-C in the command line.)
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="magic-is-for-children">1.4 Magic is for children - so what happened?</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
Every Dart application starts in a <code class="highlighter-rouge">main</code> function. In most languages, the program will terminate once main is done executing, but Dart is not most languages. Instead, if there are any open <code class="highlighter-rouge">Stream</code>s still listening for events, the program will continue to run after main has finished. Therefore, main is more of a ‘start’ than anything else. Your <code class="highlighter-rouge">main</code> function creates an instance of <code class="highlighter-rouge">Application</code>, which opens a specific kind of <code class="highlighter-rouge">Stream</code>, an <code class="highlighter-rouge">HttpServer</code>.

When an application is started, it creates an instance of its pipeline, which is defined by its type argument; in this case, <code class="highlighter-rouge">QuizPipeline</code>. The<code class="highlighter-rouge">QuizPipeline</code> is a subclass of <code class="highlighter-rouge">ApplicationPipeline</code>, which is a subclass of <code class="highlighter-rouge">RequestHandler</code>. <code class="highlighter-rouge">RequestHandler</code>s are a very important class in<code class="highlighter-rouge">aqueduct</code> - they handle requests by either responding to them or passing them on to another <code class="highlighter-rouge">RequestHandler</code>. There are lots of types of <code class="highlighter-rouge">RequestHandler</code>s built-in to <code class="highlighter-rouge">aqueduct</code> - including <code class="highlighter-rouge">Router</code>, <code class="highlighter-rouge">HTTPController</code> and <code class="highlighter-rouge">Authenticator</code>. (You may also build your own pretty easily.)

With the exception of routers, <code class="highlighter-rouge">RequestHandler</code>s are chained through their <code class="highlighter-rouge">next</code> method. This method takes the next <code class="highlighter-rouge">RequestHandler</code> as an argument and returns that same <code class="highlighter-rouge">RequestHandler</code> as a result. This allows for chaining together of <code class="highlighter-rouge">RequestHandlers</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">handlerA
	.next(handlerB)
	.next(handlerC)
	.next(handlerD);</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
This chaining is <em>always</em> declared in the <code class="highlighter-rouge">addRoutes</code> method of a pipeline. Every argument for <code class="highlighter-rouge">next</code> must be either an instance of <code class="highlighter-rouge">RequestHandler</code> (or one of its subclasses) or a closure that returns a new instance of <code class="highlighter-rouge">RequestHandler</code>.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">handlerA
	.next(handlerB)
	.next(() =&gt; new HandlerC());</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
For <code class="highlighter-rouge">HTTPController</code>s, it is required that you always create a new instance for every request. That’s because the controller keeps a bit of state about the request that you can access in your handler methods. If we reused the same controller for each request, really bad things would happen - especially because more than one request can be processed at once! (This is why your <code class="highlighter-rouge">QuestionController</code> is wrapped in a closure.)

Don’t worry though, <code class="highlighter-rouge">HTTPController</code>s are marked with <code class="highlighter-rouge">@cannotBeReused</code> metadata, so if you forget to wrap it in a closure, your application will throw an error immediately and tell you to wrap it in a closure.

Routers are slightly different. Instead of having just one <code class="highlighter-rouge">next</code>, they contain a collection of “nexts”, set through the <code class="highlighter-rouge">route</code> method. <code class="highlighter-rouge">route</code>generates an instance of a <code class="highlighter-rouge">RouteHandler</code> that is added to the router. Because <code class="highlighter-rouge">RouteHandler</code> is a subclass of <code class="highlighter-rouge">RequestHandler</code>, you can chain handlers off it. If a router gets a request for which it has a matching route, it sends it to that <code class="highlighter-rouge">RouteHandler</code>, which then immediately forwards it to its <code class="highlighter-rouge">next</code>. If a router can’t find a route that matches the incoming request, it responds to the request with a 404.
[/av_textblock]

[av_image src='http://aqueduct.stablekernel.com/wp-content/uploads/sites/3/2016/08/pipelinediagram.png' attachment='49' attachment_size='full' align='center' styling='no-styling' hover='' link='' target='' caption='' font_size='' appearance='' overlay_opacity='0.4' overlay_color='#000000' overlay_text_color='#ffffff' animation='no-animation'][/av_image]

[av_textblock size='' font_color='' color='']
Therefore, an HTTP request sent to your server starts at your pipeline, gets delivered to your router, then to the route handler you created in <code class="highlighter-rouge">addRoutes</code>, which then makes its way to <code class="highlighter-rouge">QuestionController</code> before it is responded to.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="routing-and-another-route">1.5. Routing and Another Route</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
So far, we’ve added a route that matches the constant string <code class="highlighter-rouge">/questions</code>. Routers can do more than match a constant string, they can also include path variables, optional path components, regular expression matching and the wildcard character. We’ll add to the existing<code class="highlighter-rouge">/questions</code> route by allowing requests to get a specific question.

In <code class="highlighter-rouge">quiz.dart</code>, modify the code in the pipeline’s <code class="highlighter-rouge">addRoutes</code> method by adding “/[:index]” to the route.
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">  @override
  void addRoutes() {
	   router
		   .route("/questions/[:index]")
		   .next(() =&gt; new QuestionController());
	...</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
The square brackets indicate that part of the path is optional, and the colon indicates that it is a path variable. A path variable matches anything. Therefore, this route will match if the path is <code class="highlighter-rouge">/questions</code> or <code class="highlighter-rouge">/questions/2</code> or <code class="highlighter-rouge">/questions/foo</code>.

When using path variables, you may optionally restrict which values they match with a regular expression. The regular expression syntax goes into parentheses after the path variable name. Let’s restrict the <code class="highlighter-rouge">index</code> path variable to only numbers:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">  @override
  void addRoutes() {
	   router
		   .route("/questions/[:index(d+)]")
		   .next(() =&gt; new QuestionController());
	...</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Now, there are two types of requests that will get forwarded to a <code class="highlighter-rouge">QuestionController</code> - a request for all questions (<code class="highlighter-rouge">/questions</code>) and and a request for a specific question at some index (<code class="highlighter-rouge">/questions/1</code>). We need to add a new handler method to <code class="highlighter-rouge">QuestionController</code>:
[/av_textblock]

[av_textblock size='' font_color='' color='']
<pre class="prettyprint lang-dart" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">class QuestionController extends HTTPController {
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
}</pre>
[/av_textblock]

[av_textblock size='' font_color='' color='']
Make sure you’ve stopped the application from running, and then run the application again. In your browser, enter http://localhost:8000/questions and you’ll get the list of questions. Then, enter http://localhost:8000/questions/0 and you’ll get the first question. If you enter an index not within the list of questions or something other than an integer, you’ll get a 404.

Now, there isn’t any magic here. There is an instance of <code class="highlighter-rouge">Request</code> that represents each request as it passes through different <code class="highlighter-rouge">RequestHandler</code>s. When a route has a path variable and a request comes in that matches that path variable, the router will extract that path variable and store it in the <code class="highlighter-rouge">Request</code>’s <code class="highlighter-rouge">path</code> property (which is just a glorified <code class="highlighter-rouge">Map</code>). The key of the path variable in the <code class="highlighter-rouge">path</code> will be the name of the path variable configured in the route (in this case, ‘index’). The value is the path segment of the specific request.

As you know, an <code class="highlighter-rouge">HTTPController</code> already looks at the HTTP method of an incoming request to determine which handler method to use. When the request has path variables, the <code class="highlighter-rouge">HttpController</code> also looks at the arguments to each of your handler methods. It then looks at the name of the argument - <code class="highlighter-rouge">getQuestionAtIndex</code> has an argument named <code class="highlighter-rouge">index</code> - and if that argument name matches the key of the path variable in <code class="highlighter-rouge">route</code>, it selects that method to handle the request.
[/av_textblock]

[av_heading tag='h2' padding='10' heading='<span id="the-more-you-know">1.6. The More You Know: Multi-threading and Application State</span>' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[av_textblock size='' font_color='' color='']
In this simple exercise, we used a constant list of question as the source of data for the questions endpoint. For a simple getting-your-feet-wet demo, this is fine.

However, in a real application, it is important that we don’t keep any mutable state in a pipeline or any request handlers. This is for three reasons. First, it’s just bad practice - web servers should be stateless. They are facilitators between a client and a repository of data, not a repository of data themselves. A repository of data is typically a database.

Second, the way <code class="highlighter-rouge">aqueduct</code> applications are structured makes it really difficult to keep state. For example, <code class="highlighter-rouge">HTTPController</code> is instantiated each time a new request comes in. Any state they have is discarded after the request is finished processing. This is intentional - you won’t run into an issue when scaling to multiple server instances in the future, because the code is already structured to ‘statelessly’ run across a number of isolates.

Finally, isolates. <code class="highlighter-rouge">aqueduct</code> is set up to run on multiple isolates (the <code class="highlighter-rouge">numberOfInstances</code> argument for the <code class="highlighter-rouge">Application</code>’s <code class="highlighter-rouge">start</code> method). An isolate is effectively a thread that shares no memory with other isolates. If we were to keep track of state in some way, that state would not be reflected across all of the isolates running on this web server. So depending on which isolate grabbed a request, it may have different state than you might expect. Again, <code class="highlighter-rouge">aqueduct</code> forces you into this model on purpose.

Isolates will spread themselves out across CPUs on the host machine. Having multiple isolates running the same stateless web server on one machine allows for faster request handling. Each isolate also maintains its own set of resources, like database connections.
[/av_textblock]

[/av_three_fourth][/av_section][av_section min_height='' min_height_px='500px' padding='large' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='' src='' attachment='' attachment_size='' attach='scroll' position='top left' repeat='no-repeat' video='' video_ratio='16:9' overlay_opacity='0.5' overlay_color='' overlay_pattern='' overlay_custom_pattern='']
[av_one_fourth first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[/av_one_fourth][av_three_fourth min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_heading tag='h2' padding='10' heading='What Comes Next?' color='' style='blockquote modern-quote' custom_font='#ffffff' size='30' subheading_active='' subheading_size='15' custom_class=''][/av_heading]

[/av_three_fourth][av_one_full first min_height='' vertical_alignment='' space='' custom_margin='' margin='0px' padding='0px' border='' border_color='' radius='0px' background_color='' src='' background_position='top left' background_repeat='no-repeat' animation='']

[av_hr class='invisible' height='-30' shadow='no-shadow' position='center' custom_border='av-border-thin' custom_width='50px' custom_border_color='' custom_margin_top='30px' custom_margin_bottom='30px' icon_select='yes' custom_icon_color='' icon='ue808' font='entypo-fontello']

[av_codeblock wrapper_element='' wrapper_element_attributes='']

<!--HubSpot Call-to-Action Code -->
<span id="hs-cta-wrapper-6510a742-026a-47e8-8229-cddc75fc86f6" class="hs-cta-wrapper">
<span id="hs-cta-6510a742-026a-47e8-8229-cddc75fc86f6" class="hs-cta-node hs-cta-6510a742-026a-47e8-8229-cddc75fc86f6">
<!-- [if lte IE 8]>
<div id="hs-cta-ie-element"></div>
<![endif]-->
<a href="http://cta-redirect.hubspot.com/cta/redirect/706489/6510a742-026a-47e8-8229-cddc75fc86f6"><img id="hs-cta-img-6510a742-026a-47e8-8229-cddc75fc86f6" class="hs-cta-img" style="border-width: 0px;" src="https://no-cache.hubspot.com/cta/default/706489/6510a742-026a-47e8-8229-cddc75fc86f6.png" alt="CHAPTER 2: WRITING TESTS" /></a>
</span>
<script charset="utf-8" src="https://js.hscta.net/cta/current.js"></script>
<script type="text/javascript">
        hbspt.cta.load(706489, '6510a742-026a-47e8-8229-cddc75fc86f6', {});
    </script>
</span>
<!-- end HubSpot Call-to-Action Code -->
[/av_codeblock]

[/av_one_full]
[/av_section]

[av_section min_height='' min_height_px='500px' padding='default' shadow='no-border-styling' bottom_border='no-border-styling' id='' color='main_color' custom_bg='#545355' src='http://aqueduct.stablekernel.com/wp-content/uploads/sites/3/2016/08/slider-about-aqueduct.jpg' attachment='32' attachment_size='full' attach='scroll' position='center center' repeat='stretch' video='' video_ratio='16:9' overlay_enable='aviaTBoverlay_enable' overlay_opacity='0.7' overlay_color='#000000' overlay_pattern='' overlay_custom_pattern='']
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

[/av_one_fourth]
[/av_section]
