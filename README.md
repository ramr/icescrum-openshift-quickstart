icescrum-openshift-quickstart
=============================

Running iceScrum on Red Hat's OpenShift PaaS
===========================================
This git repository is a sample DIY application along with the
"orchestration" bits to help you install and run iceScrum along with Tomcat
on Red Hat's OpenShift PaaS.


Steps to get a iceScrum running on OpenShift
--------------------------------------------

Create an account at http://openshift.redhat.com/

Create a namespace, if you haven't already do so

    rhc domain create <yournamespace>

Create a diy-0.1 application (you can name it anything via -a)

    rhc app create -a scrummage -t diy-0.1  [ -g <gear-size> ]

    Note:  Using this quickstart with "bigger" (medium to large) gear sizes is
           recommended as iceScrum needs a lot of resources (cpu + memory). 

Add the mysql-5.1 cartridge to the app

    rhc cartridge add -a scrummage -c mysql-5.1

Add this `github icescrum-openshift-quickstart` repository

    cd scrummage
    git remote add upstream -m master git://github.com/ramr/icescrum-openshift-quickstart.git
    git pull -s recursive -X theirs upstream master

If you need to adjust the memory footprint, you can adjust the JVM options
appropriately when starting up Tomcat - see `.openshift/action_hooks/start`
for more details.

As an example, to use a 512MB heap and perm-generation size of 256M, you can
modify the options that Tomcat (really the JVM) uses by setting the
_JAVA_OPTIONS environment variable in `.openshift/action_hooks/start` to:

    export _JAVA_OPTIONS="-Xmx512m -XX:MaxPermSize=256M $icescrum_java_opts -Djava.awt.headless=true"


If you made changes, then commit those changes.

    git commit . -m 'Modify JVM settings'

Then push the repo to OpenShift

    git push

And here's the toughest part, you will have to wait a while for this app to
startup. Its slow as molasses in January!! So check if it has started by
tailing the log files on your app's gear.

    ssh $guid@$app-$namespace.rhcloud.com
    rhcsh$  cd $OPENSHIFT_DATA_DIR/apache-tomcat*/logs
    rhcsh$  tail -f * logs/*

And check that the iceScrum app has started and then you can
checkout your application at:

    http://scrummage-$yournamespace.rhcloud.com/icescrum

