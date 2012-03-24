XPDBG - Taint based runtime analysis for PHP
============================================

Explanation of XPDBG
--------------------

This runtime analysis toolkit is divided in 3 components:
- the tracer on the server-side
- the trace aggregator and modeler (client-side)
- the trace visualizer (client-side)

The server-side component is very raw:
- Using Xdebug for PHP (with provided configuration)
- Spits out the traces in one file (location to provide by the user)

The trace aggregator performs the following actions:
- Connects in SSH to the server, and tails the trace file
- It queries the SQLite database of taint names. In this context, taints are essentially
  needles that need to be followed and trace in the entire application
- Aggregates the traces of requests in one model (graph)
- Generates a JSON file with slices of the graph that contains, or interact with the 
  taints (or needles)

The trace visualizer:
- Browser based visualization
- Only works on Firefox/Opera/IE (sorry, no Chrome)
- Loads the local JSON file every N seconds, and displays interesting traces
- Traces are displayed in two locations:
  - partial traces in the main view
  - actual xdebug trace is also dumped


Executing XPDBG
---------------

Example of command line:
  $ xpdbg.py --host foobar.example.com 
             --username dummy 
             --remote /tmp/xdebug/trace.2043925204.xt

Options:
  host         server to connect using SSH
  username     username to use for SSH connection (pwd to be prompted)
  remote       remote location (on the server) of the trace file


Setting up Xdebug
-----------------

Several options are available for Xdebug, but here is the configuration I usually
deploy:

# XDebug config
zend_extension=/usr/local/lib/php/extensions/xdebug.so

	[Xdebug]
	xdebug.collect_params = 4
	xdebug.collect_vars = 1
	xdebug.collect_return = 1
	xdebug.collect_assignments = 1
	xdebug.collect_includes = 1


	xdebug.auto_trace = 1
	xdebug.show_local_vars = 1
	xdebug.trace_options = 1
	xdebug.trace_output_dir = /tmp/xdebug
	xdebug.trace_output_name = trace.%c
	xdebug.trace_format = 0
	xdebug.show_exception_trace = 1
	xdebug.scream = 1

	xdebug.var_display_max_data=65536
	xdebug.var_display_max_depth=999
	xdebug.dump.POST=*
	xdebug.dump.GET=*
	xdebug.dump.COOKIE=*
	xdebug.dump.FILES=*
	xdebug.dump.REQUEST=*
	xdebug.dump.SERVER=REQUEST_METHOD,REQUEST_URI,QUERY_STRING,SCRIPT_URI
	xdebug.dump.SESSION=*

Please refer to the Xdebug documentation if any issues with Xdebug deployment


XPDBG dependencies
------------------

The distributed version of Xpdbg is quite old (developed in 2009). However, it should work
just fine with the current version of the following dependencies:
 - python-graph   http://code.google.com/p/python-graph
 - twisted        http://twistedmatrix.com
 - pygments       http://pygments.org



