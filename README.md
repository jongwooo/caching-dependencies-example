# Caching workflow Dependencies

Examples of caching workflow dependencies using [actions/cache](https://github.com/actions/cache).

## Node - npm

For npm, cache files are stored in `~/.npm` on Posix, or `~\AppData\npm-cache` on Windows, but it's possible to use `npm config get cache` to find the path on any platform. See [the npm docs](https://docs.npmjs.com/cli/cache#cache) for more details.

If using `npm config` to retrieve the cache directory, ensure you run [actions/setup-node](https://github.com/actions/setup-node) first to ensure your `npm` version is correct.

>Note: It is not recommended to cache `node_modules`, as it can break across Node versions and won't work with `npm ci`

```yaml
- name: Get npm cache directory
  id: npm-cache-dir
  run: |
    echo "::set-output name=dir::$(npm config get cache)"
- uses: actions/cache@v3
  id: npm-cache # use this to check for `cache-hit` ==> if: steps.npm-cache.outputs.cache-hit != 'true'
  with:
    path: ${{ steps.npm-cache-dir.outputs.dir }}
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

## Node - Yarn
The yarn cache directory will depend on your operating system and version of `yarn`. See https://yarnpkg.com/lang/en/docs/cli/cache/ for more info.

```yaml
- name: Get yarn cache directory path
  id: yarn-cache-dir-path
  run: echo "::set-output name=dir::$(yarn cache dir)"

- uses: actions/cache@v3
  id: yarn-cache # use this to check for `cache-hit` (`steps.yarn-cache.outputs.cache-hit != 'true'`)
  with:
    path: ${{ steps.yarn-cache-dir-path.outputs.dir }}
    key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
    restore-keys: |
      ${{ runner.os }}-yarn-
```

## Node - Yarn 2

The yarn 2 cache directory will depend on your config. See https://yarnpkg.com/configuration/yarnrc#cacheFolder for more info.

```yaml
- name: Get yarn cache directory path
  id: yarn-cache-dir-path
  run: echo "::set-output name=dir::$(yarn config get cacheFolder)"

- uses: actions/cache@v3
  id: yarn-cache # use this to check for `cache-hit` (`steps.yarn-cache.outputs.cache-hit != 'true'`)
  with:
    path: ${{ steps.yarn-cache-dir-path.outputs.dir }}
    key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
    restore-keys: |
      ${{ runner.os }}-yarn-
```
