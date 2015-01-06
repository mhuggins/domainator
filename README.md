# Domainator

Extract the registered domain name from a URI.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'domainator'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install domainator

## Usage

### Parsing Domains

There are two approaches to parsing domains with Domainator: `Domainator.parse`
and `Domainator#parse`.

#### Domainator.parse

Call the `Domainator.parse` method with a URI object or parseable URI string.

```ruby
Domainator.parse('http://www.google.com')
# => "google.com"

uri = URI.parse('http://maps.google.co.uk/map?foo=bar')
Domainator.parse(uri)
# => "google.co.uk"

Domainator.parse('http://10.0.0.1')
# => Domainator::NotFoundError: no matching domain for "http://10.0.0.1"
```

A second parameter can alternatively be passed to define the known extensions.
This parameter should be an `Enumerable` (optimally a `Set`) in which each
extension contains the leading dot.  By default, this value is set to
`Domainator.default_extensions`.

```ruby
extensions = %w[.com .net .co.uk].to_set

Domainator.parse('http://www.google.com', extensions)
# => "google.com"

Domainator.parse('http://www.google.ca', extensions)
# => Domainator::NotFoundError: no matching domain for "http://www.google.ca"
```

If a domain is found, then a string will be returned.  Otherwise, one of the
following errors will be raised:

* `ArgumentError`: the URI param was not passed in as a `String` or `URI`, or
  the URI param does not include a host, or the extensions param was not passed
  in as an `Enumerable`.
* `URI::InvalidURIError`: the URI was passed in as a `String` and cannot be
  parsed.
* `Domainator::NotFoundError`: the URI does not contain a valid domain name.

#### Domainator#parse

Instantiate a `Domainator` object by passing in an optional set of valid domain
extensions.  By default, this value is set to `Domainator.default_extensions`.

```ruby
domainator = Domainator.new
# => #<Domainator:0x007fda8428a8f0 @extensions=#<Set: {".ac", ".com.ac", ".gov.ac", ".mil.ac", ... }>>

domainator = Domainator.new(%w[.com .net].to_set)
# => #<Domainator:0x007fda823861c0 @extensions=#<Set: {".com", ".net"}>>
```

Once instantiated, call the `#parse` method with a URI object or parseable URI
string.

```ruby
domainator.parse('http://www.google.com')
# => "google.com"

domainator.parse('http://www.google.foo')
# => Domainator::NotFoundError: no matching domain for "http://www.google.foo"
```

### Domain Extensions

To see the list of domain extensions built into the gem, call the
`Domainator.default_extensions` method.

```ruby
Domainator.default_extensions
# => #<Set: {".ac", ".com.ac", ".gov.ac", ".mil.ac", ... }>
```

You can manually add to and remove from this list via the
[`Set#add`](http://www.ruby-doc.org/stdlib-2.1.5/libdoc/set/rdoc/Set.html#method-i-add)
([`Set#<<`](http://www.ruby-doc.org/stdlib-2.1.5/libdoc/set/rdoc/Set.html#method-i-3C-3C))
and [`Set#delete`](http://www.ruby-doc.org/stdlib-2.1.5/libdoc/set/rdoc/Set.html#method-i-delete)
methods.

```ruby
Domainator.default_extensions.include? '.foo'
# => false
Domainator.default_extensions.include? '.com'
# => true

Domainator.default_extensions << '.foo'
Domainator.default_extensions.delete('.com')

Domainator.default_extensions.include? '.foo'
# => true
Domainator.default_extensions.include? '.com'
# => false
```

## Known Issues

This gem does not currently work with domains that require right-to-left
language support.

## Contributing

1. Fork it ( https://github.com/[my-github-username]/domainator/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
