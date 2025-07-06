# tree-view
Top-down tree view of dom and example company website.

<h2>Node properties: type, tag and contents</h2>
<p>Let’s get a more in-depth look at DOM nodes.</p>

<p>In this chapter we’ll see more into what they are and learn their most used properties.</p>

<h3>DOM node classes</h3>
<p>Different DOM nodes may have different properties. For instance, an element node 
corresponding to tag &lt;a&gt; has link-related properties, and the one corresponding 
to &lt;input&gt; has input-related properties and so on. Text nodes are not the same as 
element nodes. But there are also common properties and methods between all of them, 
because all classes of DOM nodes form a single hierarchy.</p>

Each DOM node belongs to the corresponding built-in class.

The root of the hierarchy is EventTarget, that is inherited by Node, and other DOM nodes inherit from it.

Here’s the picture, explanations to follow:
<!-- image here -->
<h3>The classes are:</h3>
<ul>
  <li><span class="node1">EventTarget</span> – is the root “abstract” class for everything.<br>
    <p>Objects of that class are never created. It serves as a base, so that all DOM nodes support so-called “events”, we’ll study them later.</p>
  </li>
  <li><b>Node</b> – is also an “abstract” class, serving as a base for DOM nodes.<br>
    <p>It provides the core tree functionality: parentNode, nextSibling, childNodes and so on (they are getters). Objects of Node class are never created. But there are other classes that inherit from it (and so inherit the Node functionality).</p>
  </li>
  <li><span>Document</span>, for historical reasons often inherited by HTMLDocument (though the latest spec doesn’t dictate it) – is a document as a whole.<br>
  The document global object belongs exactly to this class. It serves as an entry point to the DOM.</li>
  <li><span class="classes">CharacterData</span> – an “abstract” class, inherited by:<br>
    <ul>
      <li><span class="node">Text</span> – the class corresponding to a text inside elements, e.g. Hello in &lt;p&gt;Hello&lt;/p&gt;.</li>
      <li><span class="classes">Comment</span> – the class for comments. They are not shown, but each comment becomes a member of DOM.</li>
    </ul>
  </li>
  <li><span class="classes">Element</span> – is the base class for DOM elements.<br>
    It provides element-level navigation like <span class="nav-item">nextElementSibling, children and searching methods like getElementsByTagName, querySelector.<br>
    A browser supports not only HTML, but also XML and SVG. So the Element class serves as a base for more specific classes: SVGElement, XMLElement (we don’t need them here) and HTMLElement.</li>
  </li>Finally, <span>HTMLElement</span> is the basic class for all HTML elements. We’ll 
    work with it most of the time.<br>
  <p>It is inherited by concrete HTML elements:</p>
    <ul>
      <li><span>HTMLInputElement</span> – the class for &lt;input&gt; elements,</li>
      <li><span>HTMLBodyElement</span> – the class for &lt;body&gt; elements,</li>
      <li><span>HTMLAnchorElement</span> – the class for &lt;a&gt; elements,</li>
      <li>…and so on.</li>
    </ul>
  </li>
</ul>
<p>There are many other tags with their own classes that may have specific properties and methods, while some elements, such as &lt;span&gt;, &lt;section&gt;, &lt;article&gt; do 
not have any specific properties, so they are instances of HTMLElement class.</p>

<p>So, the full set of properties and methods of a given node comes as the result of the 
chain of inheritance.</p>

<p>For example, let’s consider the DOM object for an &lt;input&gt; element. It belongs to HTMLInputElement class.</p>
<p>It gets properties and methods as a superposition of (listed in inheritance order):</p>
<ul>
  <li><span>HTMLInputElement</span> – this class provides input-specific properties,</li>
  <li>HTMLElement – it provides common HTML element methods (and getters/setters),</li>
  <li>Element – provides generic element methods,</li>
  <li>Node – provides common DOM node properties,</li>
  <li>EventTarget – gives the support for events (to be covered),</li>
  <li>…and finally it inherits from <span>Object</span>, so “plain object” methods like
    <span class="command">hasOwnProperty</span> are also available.</li>
</ul>
<p>To see the DOM node class name, we can recall that an object usually has the 
constructor property. It references the class constructor, and constructor.name 
is its name:</p>

<pre>alert( document.body.constructor.name ); // HTMLBodyElement</pre>

<p>…Or we can just <span>toString</span> it:</p>

<pre>alert( document.body ); // [object HTMLBodyElement]</pre>

<p>We also can use instanceof to check the inheritance:</p>

<pre>
alert( document.body instanceof HTMLBodyElement ); // true
alert( document.body instanceof HTMLElement ); // true
alert( document.body instanceof Element ); // true
alert( document.body instanceof Node ); // true
alert( document.body instanceof EventTarget ); // true
</pre>

<p>As we can see, DOM nodes are regular JavaScript objects. They use prototype-based classes for inheritance.</p>

<p>That’s also easy to see by outputting an element with <span>console.dir(elem)</span> 
in a browser. There in the console you can see <span>HTMLElement.prototype</span>, 
<span>Element.prototype</span> and so on.</p>

<!--~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~-->
<h4>console.dir(elem) versus console.log(elem)</h4>
<p>Most browsers support two commands in their developer tools: <span>console.log</span> 
and <span>console.dir</span>. They output their arguments to the console. For JavaScript 
objects these commands usually do the same.</p>
<p>But for DOM elements they are different:</p>
<ul>
  <li><span>console.log(elem)</span> shows the element DOM tree.</li>
  <li><span>console.dir(elem)</span> shows the element as a DOM object, good to explore 
    its properties.</li>
</ul>
<p>Try it on <span>document.body</span>.</p>
<!--~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~-->

<!--~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~-->
<h4>IDL in the spec</h4>
<p>In the specification, DOM classes aren’t described by using JavaScript, but a special 
Interface description language (IDL), that is usually easy to understand.</p>

<p>In IDL all properties are prepended with their types. For instance, DOMString, boolean and so on.</p>

<p>Here’s an excerpt from it, with comments:</p>
<pre>
// Define HTMLInputElement
// The colon ":" means that HTMLInputElement inherits from HTMLElement
interface HTMLInputElement: HTMLElement {
  // here go properties and methods of <input> elements

  // "DOMString" means that the value of a property is a string
  attribute DOMString accept;
  attribute DOMString alt;
  attribute DOMString autocomplete;
  attribute DOMString value;

  // boolean value property (true/false)
  attribute boolean autofocus;
  ...
  // now the method: "void" means that the method returns no value
  void select();
  ...
}
</pre>
<!--~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~-->
<h3>The “nodeType” property</h3>

<p>The nodeType property provides one more, “old-fashioned” way to get the “type” of a DOM node.</p>

<p>It has a numeric value:</p>
<ul>
  <li><span>elem.nodeType == 1 for element nodes,</li>
  <li><span>elem.nodeType == 3 for text nodes,</li>
  <li><span>elem.nodeType == 9 for the document object,</li>
  <li>there are few other values in the specification.</li>
</ul>

<p>For instance:</p>

<pre>
&lt;body&gt;
  &lt;script&gt;
  let elem = document.body;

  // let's examine: what type of node is in elem?
  alert(elem.nodeType); // 1 =&gt; element

  // and its first child is...
  alert(elem.firstChild.nodeType); // 3 =&gt; text

  // for the document object, the type is 9
  alert( document.nodeType ); // 9
  &lt;/script&gt;
&lt;/body&gt;
</pre>

