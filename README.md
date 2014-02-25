# appserver

A connect based middleware to support local development against a remote backend.

## Usage

This module can operate in two different modi. First as a stand-alone application that
is able to serve files from some configured directories. Second mode is via a node module
that can be loaded into some existing node project.

### Stand-Alone

You can install this module globally to make it available as a stand-alone command:

    npm install -g Open-Xchange-Frontend/appserver

After that, you can run `appserver --help` in a terminal to get this help:

```
$ appserver --help                                                                                                                                                            :(
Usage: appserver [OPTION]... [PATH]...

  -h,      --help           print this help message and exit
  -m PATH, --manifests=PATH add manifests from the specified path (default:
                            the "manifests" subdirectory of every file path)
           --path=PATH      absolute path of the UI (default: /appsuite)
  -p PORT, --port=PORT      listen on PORT (default: 8337)
  -s URL,  --server=URL     use an existing server as fallback
  -v TYPE, --verbose=TYPE   print more information depending on TYPE:
                            local: local files, remote: remote files,
                            proxy: forwarded URLs, all: shortcut for all three
  -z PATH, --zoneinfo=PATH  use timezone data from the specified path
                            (default: /usr/share/zoneinfo/)

Files are searched in each PATH in order and requested from the server if not
found. If no paths are specified, the default is /var/www/appsuite/.
```

Those defaults will only be used for the CLI version. This is because the appserver CLI has
it’s roots within the OX appsuite project.

### As a node module

tbd

#### Example usage with grunt-contrib-connect

An example of how to use this module with grunt-contrib-connect, see this snippet from our `Gruntfile.js`:

```
    var appserver = require('appserver');

    grunt.config('connect', {
        server: {
            options: {
                port: 8337,
                base: ['build/'],
                livereload: true,
                middleware: function (connect, options, middlewares) {
                    var config = grunt.config().local.appserver;
                    if (config.server === '') {
                        grunt.log.error('Server not specified in grunt/local.conf.json');
                        grunt.log.writeln('Hint: If this is a new setup you may want to copy the file grunt/local.conf.defaults.json to grunt/local.conf.json and change its values according to your setup.');
                        grunt.fail.fatal('Please adjust your local.conf.json');
                    }

                    config.prefixes = (config.prefixes || []).concat([options.base, options.base + '/apps/']);
                    config.manifests = (config.manifests || []).concat(options.base + '/manifests/');
                    config = appserver.tools.unifyOptions(config);

                    middlewares.push(appserver.middleware.appsload(config));
                    middlewares.push(appserver.middleware.manifests(config));
                    middlewares.push(appserver.middleware.localfiles(config));
                    middlewares.push(appserver.middleware.proxy(config));
                    return middlewares;
                }
            }
        }
    });
```

## Development

This project uses grunt as a task-runner during development. In order to get started,
run:

    npm install

This will install the development dependencies needed, to get you started.

Then you can run

    grunt watch

to start the watch task that will run all tasks needed if a certain file changes.
