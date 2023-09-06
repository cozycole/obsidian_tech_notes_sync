# Content Sniffing for Web Browsers

Notes taken from:
http://www.adambarth.com/papers/2009/barth-caballero-song.pdf

## MIME Type

MIME (Multipurpose Internet Mail Extensions) type (aka Media Type) is a label used to indicate the type of content contained within a file. It consists of two parts:

1. **Type**: general category of content. For example:

    - text: Textual content (e.g., HTML, plain text)
    - image: Image data (e.g., JPEG, PNG, GIF)
    - audio: Audio data (e.g., MP3, WAV)
    - video: Video data (e.g., MP4, AVI)
    - application: General application data (e.g., PDF, JSON)

2. **Subtype**: This further specifies the exact format or use of the content. For example:

    - plain: Plain text subtype
    - html: HTML markup subtype
    - jpeg: JPEG image subtype
    - json: JSON data subtype
    
MIME types are communciated between clients and servers through HTTP headers. Clients include an *"Accept"* header indicating the MIME types it can handle, and the server responds with the appropriate MIME type in the "Content-Type" header of the response, informining the client of the type of data it's sending.

## Content Sniffing

Approximately 1% of HTTP response lack a *Content-Type* header, indicating to the browser the MIME type of file being sent. In a competitive browser market, browsers that guesses the correct MIME type are more appealing to users than browsers that fail to render these sites. Browsers implement content sniffing algorithms to guess the MIME type to allow for a better user experience.

Even if there is a *Content-Type*, browsers still sniff it in case the server gets the type wrong.

### Upload Filters

When a user uploads a file to a Web site, the site has 3 options for assigning a MIME type to the content:

1. the Web site can use the MIME type in the *Content-Type* header
2. it can infer the MIME type from the file's extension
3. it examines the contents of the file to determine the type

Option 3 is the only safe, reliable option. Whenever the web server's assigned MIME type differs from the browsers content-sniffing algorithm, a *content-sniffing XSS attack* can occur.

## Cross Site Scripting (XSS) Attacks

In order to mount this attack, an attacker needs to find a mismatch between the benign file type a web site accepts and the browser's sniffing algorithm rendering it as HTML. For example, an attacker can create a a "chameleon" document that is both valid PostScript (for example, which is an old document scripting language created by Adobe) and HTML. See below:

```HTML
%!PS-Adobe-2.0
%%Creator: <script> ... </script>
%%Title: attack.dvi
```

Suppose a malicious author uploads this to a webserver, which the webserver accepts as valid PostScript, a benign file type. The browser content-sniffing algorithm detects it as HTML which overrides the Content-Type header of the response. 

So whenever a user views this file, it executes the Javascript within the script tag. This lets the attacker run a malicious script in the webserver's security origin (i.e., evade any CORS policies). This script could steal the user's password or transact on behalf of the user.

Stopped reading at page 6 of paper


