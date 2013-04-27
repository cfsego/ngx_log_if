#README#
ngx_log_if_module is an independent module which allows you to control when not to write down
access log, similar to the "CustomLog [env=XXX]" directive in Apache.

##Directive##
    Syntax:  access_log_bypass_if (condition) [and]
    Default: -
    Scope:   main/srv/loc
"access_log_bypass_if" defines the condition that nginx will not write down access log in case
it is true. The condition is enclosed by brackets... In brief, the syntax of condition in
"access_log_bypass_if" is the same as it in the "if" directive. If flag "and" is added, this
line will work together will the next line.

##Example##
This directive allows you to define multiple conditions in the form of using the directive in
multiple times in the same block. For example:

    server {
        access_log_bypass_if ($status = 400);
        access_log_bypass_if ($host ~* 'nolog.com');
        access_log_bypass_if ($uri = 'status.nginx') and;
        access_log_bypass_if ($status = 200);
    }

In the case, nginx will not write access log when the status code is 400, or when the host is
'nolog.com', or when the uri is 'status.nginx' and the status code is 200.

However, if you define them both in the blocks in the father child relationship, the child block
will not inherit and merge the configuration in parent block, of course. FOr example:

    server {
        access_log_bypass_if ($status = 400);
    
        location / {
            access_log_bypass_if ($host ~* 'nolog.com');
        }
    }

In this case above, in location "/", only "($host ~* 'nolog.com')" is available, but "($status = 400)"
is ignored and makes no difference.

#CHANGES#
1.0.3     2013-04-23    bugfixed: https://github.com/cfsego/ngx_log_if/issues/1, thanks to yixing

1.0.2     2012-10-17    feature: flag "and" added

1.0.1     2012-10-10    bugfixed: uninitialized value is used

1.0.0     2012-09-18    initial version
