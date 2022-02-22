# swift-builders

Helper library for building Swift projects as Nix derivations. These provide an alternative to SwiftPM for
projects what aspire to be a better citizen in Nix ecosystem.

## APIs

APIs from _swift-builders_ are acccessable in the `lib` namespace when used as flake inputs. For example, you
could use `supportedPlatforms` along with `flake-utils` to ensure all OSes where Swift is available are built
for your package:

```nix
{
  inputs = {
    flake-utils.url = "github:numtide/flake-utils";
    swift.url = "github:dduan/swift-builders";
  };
  output = { self, flake-utils, swift }:
    flake-utils.lib.eachSystem swiftPlatforms (system:
      # define your package content here...
    );
}
```

### `swiftPlatforms`

A list of platforms where Swift is available. An example value in the list could be `x86_64-linux`. The list
is useful when combined with `github:numtide/flake-utils`'s [flake-utils.lib.eachSystem][]:


```nix
{
  inputs = {
    flake-utils.url = "github:numtide/flake-utils";
    swift.url = "github:dduan/swift-builders";
  };
  output = { self, flake-utils, swift }:
    flake-utils.lib.eachSystem swiftPlatforms (system:
      # define your package content here...
    );
}
```

[flake-utils.lib.eachSystem]: https://github.com/numtide/flake-utils#eachsystem---system---system---attrs

The first parameter of all the `mk*` APIs documented below is a instance of nixpkgs. with the input
`nixpkgs.url = "github:nixos/nixpkgs";`, this value would be `nixpkgs.legacyPackages.x86_64-linux` for 64bit
Linux. The second parameter is an attribute set that describes information regarding your source target. The
details of each attribute set is documented below.

### `mkDynamicCLibrary`

Build a Nix derivation containing a library importable by Swift source code. The source for this target must
contain a `include` subdirectory with the neccessary public header and modulemap (similar to SwiftPM's
target).

#### Parameters

- __version__: A version for the target.
- __src__: derivation that contains the source code.
- __target__: name of the target. This becomes the value for this target used with `import` statements in
  consumer code.
- __srcRoot__: *optional*. The root path to search for source code for this target. All C files in this
  directory and its subdirectories are considered part of this target. Defaults to `Sources/${target}`.
- __buildInputs__: *optional*. A list of runtime dependencies required for this target. Each item must be
  a derivation built by `swift-builders`. This value gets forwarded as `buildInputs` of Nix [derivation][].
- __patchPhase__: *optional*. Logic for patching the source code prior to compilation. This is useful when the
  existing project structure is non-stardard (some source file is outside of `srcRoot`). This value gets
  forwarded as `patchPhase` of Nix [derivation][].
- __installPhase__: *optional*. The `installPhase` value for [derivation][]. By default, all built artifacts
  are put in `$out/lib` and `$out/swift`.
- __extraCompilerFlags__: *optional*. Additional compiler flags for `clang` compiler.

### `mkDynamicLibrary`

Build a Nix derivation containing a library importable by Swift source code.

#### Parameters

- __version__: A version for the target.
- __src__: derivation that contains the source code.
- __target__: name of the target. This becomes the value for this target used with `import` statements in
  consumer code.
- __srcRoot__: *optional*. The root path to search for source code for this target. All Swift files in this
  directory and its subdirectories are considered part of this target. Defaults to `Sources/${target}`.
- __buildInputs__: *optional*. A list of runtime dependencies required for this target. Each item must be
  a derivation built by `swift-builders`. This value gets forwarded as `buildInputs` of Nix [derivation][].
- __patchPhase__: *optional*. Logic for patching the source code prior to compilation. This is useful when the
  existing project structure is non-stardard (some source file is outside of `srcRoot`). This value gets
  forwarded as `patchPhase` of Nix [derivation][].
- __installPhase__: *optional*. The `installPhase` value for [derivation][]. By default, all built artifacts
  are put in `$out/lib` and `$out/swift`.
- __extraCompilerFlags__: *optional*. Additional compiler flags for `swiftc` compiler.

### `mkDynamicLibrary`

Build a Nix derivation containing a executable.

#### Parameters

- __version__: A version for the target.
- __src__: derivation that contains the source code.
- __target__: name of the target. This becomes the value for this target used with `import` statements in
  consumer code.
- __srcRoot__: *optional*. The root path to search for source code for this target. All Swift files in this
  directory and its subdirectories are considered part of this target. Defaults to `Sources/${target}`.
- __buildInputs__: *optional*. A list of runtime dependencies required for this target. Each item must be
  a derivation built by `swift-builders`. This value gets forwarded as `buildInputs` of Nix [derivation][].
- __patchPhase__: *optional*. Logic for patching the source code prior to compilation. This is useful when the
  existing project structure is non-stardard (some source file is outside of `srcRoot`). This value gets
  forwarded as `patchPhase` of Nix [derivation][].
- __installPhase__: *optional*. The `installPhase` value for [derivation][]. Useful when you need the
  executable to have a deferent name than `target`. By default, all built artifacts are put in `$out/lib`,
  `$out/bin`, and `$out/swift`.
- __extraCompilerFlags__: *optional*. Additional compiler flags for `swiftc` compiler.

[derivation]: https://nixos.org/manual/nix/stable/expressions/derivations.html
