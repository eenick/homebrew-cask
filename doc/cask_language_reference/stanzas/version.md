# version

`version`, while related to the app’s own versioning, doesn’t have to follow it exactly. It is common to change it slightly so it can be [interpolated](https://en.wikipedia.org/wiki/String_interpolation#Ruby_/_Crystal) in other stanzas, usually in `url` to create a Cask that only needs `version` and `sha256` changes when updated. This can be taken further, when needed, with [ruby String methods](https://ruby-doc.org/core/String.html).

For example:

Instead of

```ruby
version "1.2.3"
url "https://example.com/file-version-123.dmg"
```

We can use

```ruby
version "1.2.3"
url "https://example.com/file-version-#{version.delete('.')}.dmg"
```

We can also leverage the power of regular expressions. So instead of

```ruby
version "1.2.3build4"
url "https://example.com/1.2.3/file-version-1.2.3build4.dmg"
```

We can use

```ruby
version "1.2.3build4"
url "https://example.com/#{version.sub(%r{build\d+}, '')}/file-version-#{version}.dmg"
```

## version :latest

The special value `:latest` is used on casks which:

1. `url` doesn’t contain a version.
2. Having a correct value to `version` is too difficult or impractical, even with our automated systems.

Example: [spotify.rb](https://github.com/Homebrew/homebrew-cask/blob/f56e8ba057687690e26a6502623aa9476ff4ac0e/Casks/spotify.rb#L2)

## version methods

The examples above can become hard to read, however. Since many of these changes are common, we provide a number of helpers to clearly interpret otherwise obtuse cases:

| Method                   | Input              | Output             |
|--------------------------|--------------------|--------------------|
| `major`                  | `1.2.3-a45,ccdd88` | `1`                |
| `minor`                  | `1.2.3-a45,ccdd88` | `2`                |
| `patch`                  | `1.2.3-a45,ccdd88` | `3-a45`            |
| `major_minor`            | `1.2.3-a45,ccdd88` | `1.2`              |
| `major_minor_patch`      | `1.2.3-a45,ccdd88` | `1.2.3-a45`        |
| `minor_patch`            | `1.2.3-a45,ccdd88` | `2.3-a45`          |
| `before_comma`           | `1.2.3-a45,ccdd88` | `1.2.3-a45`        |
| `after_comma`            | `1.2.3-a45,ccdd88` | `ccdd88`           |
| `dots_to_hyphens`        | `1.2.3-a45,ccdd88` | `1-2-3-a45,ccdd88` |
| `no_dots`                | `1.2.3-a45,ccdd88` | `123-a45,ccdd88`   |

Similar to `dots_to_hyphens`, we provide all logical permutations of `{dots,hyphens,underscores}_to_{dots,hyphens,underscores}`. The same applies to `no_dots` in the form of `no_{dots,hyphens,underscores}`, with an extra `no_dividers` that applies all of those at once.

Finally, there are `before_colon` and `after_colon` that act like their `comma` counterparts. These four are extra special to allow for otherwise complex cases, and should be used sparingly. There should be no more than one of `,` and `:` per `version`. Use `,` first, and `:` only if absolutely necessary.
