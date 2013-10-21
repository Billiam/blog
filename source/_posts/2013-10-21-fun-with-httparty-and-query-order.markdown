---
layout: post
title: "Fun with Httparty and Query Order"
date: 2013-10-21 14:15
comments: false
categories: ruby httparty 
sharing: false
---
Ran into an issue this weekend with  [httparty](https://github.com/jnunemaker/httparty), and an API (mapquest) that was sensitive to query parameter order. Specifically, when batch geocoding locations, mapquest expects a query string like: `?location=foo&location=bar&location=baz`, and will return geocode results in the order received.

I noticed the issue when an api call for a US city returned a result in Ireland.

Httparty has two methods of encoding query parameters. The default, rails-friendly, way converts 

```ruby
:param => ['bar','baz']
```
into 
``` html
?param[]=foo&param[]=bar
```

This can be disabled by calling `disable_rails_query_string_format`, which replaces the default query string normalizer with [this proc](https://github.com/jnunemaker/httparty/blob/c06746e4abc2c5a95f62bf8ea3b88bb37a93f4b2/lib/httparty/request.rb#L17-L27)

``` ruby request.rb https://github.com/jnunemaker/httparty/blob/c06746e4abc2c5a95f62bf8ea3b88bb37a93f4b2/lib/httparty/request.rb#L17-L27
NON_RAILS_QUERY_STRING_NORMALIZER = Proc.new do |query|
  Array(query).map do |key, value|
    if value.nil?
      key.to_s
    elsif value.is_a?(Array)
      value.map {|v| "#{key}=#{URI.encode(v.to_s, Regexp.new("[^#{URI::PATTERN::UNRESERVED}]"))}"}
    else
      HashConversions.to_params(key => value)
    end
  end.flatten.sort.join('&')
end
```

Different version of ruby (even different 1.8.7 versions) will sort the result of 

```ruby
Array({:key => :value})
```

differently. A sort was added at the end of the normalizer proc to ensure that the query string is consistent across versions, likely for testing. This works, but has the side effect of also shuffling the order of array value elements.

## Solutions 

 * Presort query parameters to avoid surprises,
 * use an alternate proc for query normalization (I just removed the sort entirely in my app), 
 * submit request as json (or other format)
 * or wait until it's fixed upstream.

Httparty pull request can be found here: https://github.com/jnunemaker/httparty/pull/245
