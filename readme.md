# About SaxonJS

SaxonJS is an XSLT 3.0 processor written mainly in JavaScript, and partly in XSLT. SaxonJS 2 is available for two JavaScript environments: the browser, and Node.js. We'll focus here on Node.js.
   
The npm package **saxon-js** provides a JavaScript API that allows you to:
   
   * Invoke XSLT 3.0 transformations
   * Run XPath 3.1 expressions against DOM trees
   * Build DOM trees from XML resources held in filestore or accessed over HTTP
   * Serialize DOM trees to XML or HTML
   
A command-line interface for XSLT 3.0 transformation is provided in a separate package, **xslt3**.

[Full documentation is available](http://www.saxonica.com/saxon-js/documentation/index.html), delivered using SaxonJS 2 running in your browser.

## About XSLT 3.0 and XPath 3.1

XSLT 3.0 and XPath 3.1 are the latest versions of the XSLT and XPath specifications from W3C.

The 1.0 versions of these languages are widely known and widely used, but they are now twenty years old, and the standards have come a long way since then.

Version 2 of the specs introduced, among other things:

* Grouping instructions (`<xsl:for-each-group>`)
* Regular expressions
* Date-and-time arithmetic and formatting
* A wider range of data types
* User-written functions in XSLT

Version 3 added:

* Support for JSON, including maps and arrays
* Higher-order functions
* Support for HTML5
* Inline text templates: `<p>Written by {@author} on {@date}</p>`
* Separately-compiled XSLT packages

SaxonJS implements all of the mandatory parts of the XSLT 3.0 and XPath 3.1 specifications, and most of the optional parts. The only major things it leaves out are:

* Schema-awareness
* Streaming
* XSLT Packages (but see below)

## Saxon compile time and run time

The Saxon product effectively contains a compile time and a run time component. The compiler takes XSLT source code as input, and generates a Stylesheet Export File (SEF), which is an intermediate representation of the compiled and optimized stylesheet in a custom JSON format. In fact, Saxonica offers two XSLT compilers:

* The <b>XJ compiler</b> is an XSLT-to-SEF compiler written in Java, and delivered as part of the Saxon-EE product, running under Java.
* The <b>XX compiler</b> is an XSLT-to-SEF compiler written in XSLT, making calls on an XPath parser written in JavaScript. It is delivered as part of SaxonJS, and can be invoked using the **xslt3** command line.

SaxonJS will load SEF files generated by either compiler. For production work the XJ compiler is a better choice: it is faster, it produces better diagnostics, it generates smaller and faster run-time code, and it has fewer restrictions. But the XX compiler is available at no cost.

XSLT 3.0 packages are partially supported: SaxonJS offers run-time support, but to compile them you will need the XJ compiler.

## About Saxonica

SaxonJS is developed by Saxonica, a company created in 2004 by Michael Kay, who was the editor of the XSLT 2.0 and 3.0 specifications in W3C. The original Saxon product on the Java platform has been continually developed for over 20 years, and has acquired a reputation for standards conformance, performance, and reliability.

SaxonJS takes many of the successful design ideas of the original Java-based product and re-implements them in JavaScript. Although the Node.js version of the product is brand new, it benefits not only from the experience of the team creating it, but also from the existence of a vast test suite, ensuring a high level of reliability.

## Examples

### Compiling a stylesheet

To compile a stylesheet held in `books.xsl` to a SEF file in `books.sef.json`, to be run either in the browser or Node.js, use the command line:

**`xslt3 -xsl:books.xsl -export:books.sef.json -t -ns:##html5`**

Here `-t` requests progress messages on the console, and `-ns:##html5` requests customized handling of namespaces in the HTML DOM (which is non-standard according to the W3C standards, but mimics the way XSLT 1.0 processors behave in the browser).

### Running a transformation from the command line

To execute the stylesheet against a source document `books.xml` and write the output to a file `books.html`, use:

**`xslt3 -xsl:books.sef.json -s:books.xml -o:books.html -t`**

Alternatively the XSLT stylesheet can be run directly against the source document, using:

**`xslt3 -xsl:books.xsl -s:books.xml -o:books.html -t`**


### Running a transformation from JavaScript

To run the same transformation from JavaScript within a Node.js application, sending the result to an HTTP response object `response`, use:

```
            SaxonJS.transform({
                stylesheetFileName: "books.sef.json",
                sourceFileName: "books.xml",
                destination: "serialized"
            }, "async")
            .then (output => {
                response.writeHead(200, {'Content-Type': 'text/html'});
                response.write(output.principalResult);
                response.end();
            })
```

### Running XPath from JavaScript

The following example parses an XML document, executes a simple XPath expression, and serializes the result:

```
            SaxonJS.getResource({
                location: '../../src/test.xml',
                type: "xml"
            }).then(doc => {
                const result = SaxonJS.XPath.evaluate("//test[@id='abc']", doc);
                const output = SaxonJS.serialize(result, {method: "xml", indent: true, "omit-xml-declaration":true});
                console.log(output);
            })
```
The next example does the same with JSON input:

```
            SaxonJS.getResource({
                location: '../../src/test.json',
                type: "json"
            }).then(doc => {
                const result = SaxonJS.XPath.evaluate("?cities?*[?name='Berlin']", doc);
                const output = SaxonJS.serialize(result, {method: "json", indent: true});
                console.log(output);
            })
```

## SaxonJS Components

SaxonJS for Node.js includes the following deliverables, available free of charge (but the license is <b>not</b> open source):

* <b>saxon-js package (SaxonJS2N.js)</b>: this includes all the code needed to compile and execute XSLT 3.0 stylesheets under Node.js, including an API enabling applications to invoke stylesheet execution. The JavaScript module is around 3.7Mb in size.

* <b>xslt3 package (xslt3.js)</b>: this small module provides a command line interface, allowing XSLT 3.0 transformations to be compiled and run from a terminal/console. It makes use of the services provided by the saxon-js (SaxonJS2N.js) package.</p>

## SaxonJS in the Browser

SaxonJS is also available to run in the browser, with innovative extensions allowing user interaction to be handled entirely from declarative XSLT code, without any need for low level Javascript event handling. For details, see the [Saxonica web site](http://www.saxonica.com/saxon-js/index.xml).
               
## Support and Services

SaxonJS is offered free of charge, but for serious users, a chargeable service package is available for an annual fee. The service package includes access to source code and build tools, debugging aids, and other resources such as tutorials and add-on utilities that may be published from time to time. For details, see the [Saxonica web site](http://www.saxonica.com/saxon-js/index.xml).
                  
All users are welcome to use the public [support site](http://saxonica.plan.io) for reporting issues and seeking help (registration required). In addition, many questions are asked and answered on [StackOverflow](https://stackoverflow.com): please use the **saxon-js** tag. 


