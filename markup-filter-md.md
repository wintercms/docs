# | md

The `| md` filter converts the value from Markdown to HTML format.

    {{ '**Text** is bold.' | md }}

The above will output the following:

    <p><strong>Text</strong> is bold.</p>

# | md_line

The `| md_line` filter converts the value from Markdown to HTML format, as inline element.

    {{ '**Text** is bold.' | md_line }}

The above will output the following:

    <strong>Text</strong> is bold.

# | md_safe

The `| md_safe` filter converts the value from Markdown to HTML format, preventing `<code>` blocks caused by indentation.

    {{ '    **Text** is bold.' | md_safe }}

The above will output the following:

    <p><strong>Text</strong> is bold.</p>

instead of

    <pre><code><strong>Text</strong> is bold.</p></code></pre>
