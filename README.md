### Tandem's modifications:

*Description and metatags*

You can use the data fields or free text the description and tags:
```
seo:
  description: byline
  metatags: tags
```
OR
```
seo:
  description: Super TRILLY TRONIC
  metatags: sup, trill, tronic
```

*Twitter*

You can add Twitter tag definition as well.  The defaults are defined in the node module with the exception of the image, which is defined in metalsmith.json:

```
twitter:
    siteurl: https://thinktandem.io
    card: summary
    site: @ThinkTandem
    image: whatever
```
The image uses either the image tag in the md or the image defined in metalsmith.json.

The title and description are taken from the seo title and description, I didn't think we needed to get that granular with it.

*Metatags*

Slap this in your html.html (or similar header file template):

```html
<title>{{config.title}} | {{title}}</title>
<meta name="robots" content="{{seo.robots}}">
<link rel="canonical" href="{{seo.canonical}}" />
<meta property="og:url" content="{{seo.canonical}}" />
<meta property="og:title" content="{{seo.ogp.title}}" />
<meta name="description" content="{{seo.description}}">
<meta property="og:description" content="{{seo.description}}" />
<meta name="keywords" content="{{seo.keywords}}" />
{% if seo.ogp.type %}
<meta property="og:type" content="{{seo.ogp.type}}" />
{% else %}
<meta property="og:type" content="website" />
{% endif %}
{% if seo.ogp.image %}
<meta property="og:image" content="{{seo.ogp.image}}" />
{% endif %}
<meta name="twitter:card" content="{{twitter.card}}" />
<meta name="twitter:site" content="{{twitter.site}}" />
<meta name="twitter:title" content="{{title}}" />
<meta name="twitter:description" content="{{seo.description}}" />
<meta name="twitter:image" content="{{twitter.image}}" />
```

metalsmith-seo-checker
==========================

This plugin provides a simple way to ensure that front matter values regarding SEO are valid.

You can provide defaults for SEO parameters, or mark them as required by setting them to true.
This should be combined with something in your templating language that places these values
where they belong. An example header that uses handlebars templating can be seen at
`/examples/seo-header.html`

This plugin manipulates values within an seo object inside of your front-matter, with one
exception. The one exception is the title property which is expected to be set on the base
front matter object.

SEO Checker will also check for a `private` flag on the front matter. If this flag is found
and there is no `seo.robots` provided `seo.robots` will be set to 'noindex, nofollow'.

With this set of options:

```js
{
    ignoreFiles: ['special.html'],
    trailingSlash: true, // Append a trailing slash to the canonical url
    lengths: {
        title: 60, // This is the default value
        description: 160 // This is the default value
    },
    seo: {
        title: true, // This is the default value
        description: 'Foo Bar Baz', // There is no default for this
        robots: 'index, follow' // This is the default value
    },
    ogp: {
        defaultType: 'website', // This is the default value
        defaultImage: 'www.example.org/myImage.png', // The default value for this is false
        ignoreMissingImage: false // This is the default value
    }
}
```

The `lengths` block sets up max string length checks on different fields. The `seo` block
defines which fields are required, or have a default value. Finally the `ogp` block
defines attribute checks that are specific to the [Open Graph Protocol](http://ogp.me).

In english this config block is ensuring that we have a title on the base front matter and
that it is no longer than 60 characters. We then provide a default description of 'Foo Bar
Baz' and ensure that all `seo.descriptions` are less than 160 characters. We are also
ensuring that any page without a `seo.robots` field defined will have 'index, follow' set.
Finally the ogp config ensures that for any page that has no `seo.ogp.type` we set the type
to 'website', and for any page without an image we can provide a default image. The
`ignoreMissingImage` config determines how the plugin will react to there being no
`seo.ogp.image` (after applying the default image provided).
