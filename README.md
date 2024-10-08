# flake.nix

![CI state](https://github.com/daeuniverse/flake.nix/actions/workflows/lint.yaml/badge.svg)
[![built with garnix](https://img.shields.io/endpoint.svg?url=https%3A%2F%2Fgarnix.io%2Fapi%2Fbadges%2Fdaeuniverse%2Fflake.nix%3Fbranch%3Dmain)](https://garnix.io)

## Use with flake

1. Import nixosModule.

```nix
# flake.nix

{
  inputs.daeuniverse.url = "github:daeuniverse/flake.nix";
  # ...

  outputs = {nixpkgs, ...} @ inputs: {
    nixosConfigurations.HOSTNAME = nixpkgs.lib.nixosSystem {
      modules = [
        inputs.daeuniverse.nixosModules.dae
        inputs.daeuniverse.nixosModules.daed
      ];
    };
  }
}
```

2. Enable dae or daed module.

> To see full options, check `dae{,d}/module.nix`.

```nix
# nixos configuration module
{
  # ...

  services.dae = {
      enable = true;

      openFirewall = {
        enable = true;
        port = 12345;
      };

      /* default options

      package = inputs.daeuniverse.packages.x86_64-linux.dae;
      disableTxChecksumIpGeneric = false;
      configFile = "/etc/dae/config.dae";
      assets = with pkgs; [ v2ray-geoip v2ray-domain-list-community ];

      */

      # alternative of `assets`, a dir contains geo database.
      # assetsPath = "/etc/dae";
  };
}
```

```nix
# nixos configuration module
{
  # daed - dae with a web dashboard
  services.daed = {
      enable = true;

      openFirewall = {
        enable = true;
        port = 12345;
      };

      /* default options

      package = inputs.daeuniverse.packages.x86_64-linux.daed;
      configDir = "/etc/daed";
      listen = "127.0.0.1:2023";

      */
  };
}
```

## Globally install packages

```nix
# nixos configuration module
{
  environment.systemPackages =
    with inputs.daeuniverse.packages.x86_64-linux;
      [ dae daed ];
}
```

## Nightly build

If you would like to get a taste of new features and do not want to wait for new releases, you may use the `nightly` (`unstable` branch) flake. The `nightly` flake is always _**up-to-date**_ with the upstream `dae` and `daed` (sync with the `main` branch) projects. Most of the time, newly proposed changes will be included in PRs, will be fully tested, and will be exported as cross-platform executable binaries in builds (GitHub Action Workflow Build). If you would like to test out any unpublished changes, feel free to use the `experiment` branch which is pinned to a specific commit in a feature branch from the upstream repositories.

> [!WARNING]
> Note that newly introduced features can sometimes be buggy; use at your own risk. However, we still highly encourage you to check out our latest builds as it may help us further analyze features stability and resolve potential bugs accordingly.

Adopt nightly flake

```nix
# flake.nix
{
  # unstable
  inputs.daeuniverse.url = "github:daeuniverse/flake.nix/unstable";
  # OR
  # experiment
  inputs.daeuniverse.url = "github:daeuniverse/flake.nix/experiment";
  # ...
}
```

## Release build

If you prefer to use a more stable version of our software, you can use the `release` branch. This branch is designated for our official releases. We create release tags based on this branch to ensure stability and reliability.

Whenever there is a new release from the upstream projects (`dae` and `daed`), we will also create a corresponding release tag in our repository, such as `dae-v0.7.0`. These tags represent the stable versions of our software, thoroughly tested and ready for production use.

Adopt release flake

```nix
# flake.nix
{
  # latest release
  inputs.daeuniverse.url = "github:daeuniverse/flake.nix/release";
  # OR
  # specific tag
  inputs.daeuniverse.url = "github:daeuniverse/flake.nix?tag=<tag>";
  # ...
}
```

This way, users can choose the `release` branch and tags for stable, `production-ready` versions.

## Binary cache

We use garnix cache and provide both `x86_64-linux` and `aarch64-linux` build products.

To setup the garnix cache:

```nix
nix.settings = {
  substituters = ["https://cache.garnix.io"];
  trusted-public-keys = [
    "cache.garnix.io:CTFPyKSLcx5RMJKfLo5EEPUObbA78b0YQ2DTCJXqr9g="
  ];
};
```

## License

[ISC](./LICENSE) © 2023-2024 @daeuniverse
