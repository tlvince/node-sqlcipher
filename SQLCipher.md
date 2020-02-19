# Including SQLCipher

**Note:** All these steps have already been performed in this repository. This is just for reference to e.g. change the SQLCipher version.

## Step 1: Get the SQLCipher source

Clone the sqlcipher repo, and build the amalgamation source.

```
git clone git@github.com:sqlcipher/sqlcipher.git
cd sqlcipher
./configure
make sqlite3.c
```

The above produces 4 files of interest:

```
sqlite3.c
sqlite3.h
shell.c # optional
sqlite3ext.h # optional
VERSION # optional
```

Copy these files to a new folder, `sqlcipher-amalgamation-<version>`.

## Step 2: Get OpenSSL libraries

NodeJS typically includes OpenSSL. However, for Electron on Windows, we need to bundle a copy.

Download OpenSSL 1.1.1x from https://www.openssl.org/source/, both 32-bit and 64-bit versions. Install locally, then copy these files:

```
libcrypto.lib
libssl.lib
ossl_static.pdb
```

Place these files in `sqlcipher-amalgamation-<version>/OpenSSL-Win32` and `sqlcipher-amalgamation-<version>/OpenSSL-Win64`.

Copy the header files (include folder) to `sqlcipher-amalgamation-<version>/openssl-include/openssl`.

## Step 3: Build the archive

Archive the folder as `deps/sqlcipher-amalgamation-<version>.tar.gz`, and update the version number in `common-sqlite.gypi` (must be the same).

## Step 4: Test the build

Run:

```sh
./node_modules/.bin/node-pre-gyp install --build-from-source
```

Then run the tests:

```sh
npm run test
```


# Notes

The OpenSSL files are specifically required for Electron, which doesn't bundle OpenSSL like NodeJS does. The header and .lib files are required at compile-time, and `libeay32.dll` and `mscvr120.dll` are required at runtime. We bundle it with the library, so the user does not need to manually install OpenSSL.

`deps/sqlite3.gyp` has been modified from the original node-sqlite3 one to:
 * Use the bundled OpenSSL on Windows.
 * Add additional define statements required by SQLCipher.


