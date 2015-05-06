
An appender to send formatted logging event strings to a specified managment
host (typically, a MLM of some sort, but could also be an SNMP management
console) in the form of an SNMP trap.
<p>
This appender does not attempt to provide full access to the SNMP API.  In
particular, use of this appender does not make an SNMP agent out of the
calling application.  You cannot use this appender as an interface to do SNMP
GET or SET calls  -- all it does is pass on your logging event as a TRAP.
<p>
This appender uses a helper class which serves as the interface to the SNMP
sub-system.  This class must implement the SnmpTrapSenderFacade interface.  The
concrete implementation class you want to use must be specified in your properties
file -- see the examples below.  The implementation class must provide a
parameterless constructor -- this is the constructor that Log4J will use to
instantiate the class using the class name in the properties file.<br>
<br>
There are three implementation classes provided with this appender; the JoeSNMPTrapSender,
the WengsoftSNMPTrapSender and the NetSnmpCommandLineTrapSender classes.
If you fail to specify an implementation in your properties file, or if there
is a problem finding, loading or instantiating the implementation that you do specify,
the appender will fall back to using the JoeSNMPTrapSender implementation as a default.<p>

You can and should, as your needs dictate, write your own implementation of
SnmpTrapSenderFacade, using the underlying SNMP library of your choice.  In this
case, the implementations provided with the appender should serve as adequate
examples for how this might be done.<p>

Here's a sample of what you would need in an XML configuration file to configure
this appender:<br>
<pre><code>
   &lt;appender name="TRAP_LOG" class="org.apache.log4j.ext.SNMPTrapAppender"&gt;
       &lt;param name="ImplementationClassName" value="org.apache.log4j.ext.JoeSNMPTrapSender"/&gt;
       &lt;param name="ManagementHost" value="127.0.0.1"/&gt;
       &lt;param name="ManagementHostTrapListenPort" value="162"/&gt;
       &lt;param name="EnterpriseOID" value="1.3.6.1.4.1.24.0"/&gt;
       &lt;param name="LocalIPAddress" value="127.0.0.1"/&gt;
       &lt;param name="LocalTrapSendPort" value="161"/&gt;
       &lt;param name="GenericTrapType" value="6"/&gt;
       &lt;param name="SpecificTrapType" value="12345678"/&gt;
       &lt;param name="CommunityString" value="public"/&gt;
       &lt;param name="ForwardStackTraceWithTrap" value="true"/&gt;
       &lt;param name="Threshold" value="DEBUG"/&gt;
       &lt;param name="ApplicationTrapOID" value="1.3.6.1.4.1.24.12.10.22.64"/&gt;
       &lt;layout class="org.apache.log4j.PatternLayout"&gt;
               &lt;param name="ConversionPattern" value="%d,%p,[%t],[%c],%m%n"/&gt;
       &lt;/layout&gt;
   &lt;/appender&gt;
</pre></code>
<br>
To configure an otherwise identical appender that uses a different implementation
class, you would simply change the value of the "ImplementationClassName", as
in the following example:<br>
<pre><code>
   &lt;appender name="TRAP_LOG" class="org.apache.log4j.ext.SNMPTrapAppender"&gt;
       &lt;param name="ImplementationClassName" value="org.apache.log4j.ext.WengsoftSNMPTrapSender"/&gt;
       &lt;param name="ManagementHost" value="127.0.0.1"/&gt;
       &lt;param name="ManagementHostTrapListenPort" value="162"/&gt;
       &lt;param name="EnterpriseOID" value="1.3.6.1.4.1.24.0"/&gt;
       &lt;param name="LocalIPAddress" value="127.0.0.1"/&gt;
       &lt;param name="LocalTrapSendPort" value="161"/&gt;
       &lt;param name="GenericTrapType" value="6"/&gt;
       &lt;param name="SpecificTrapType" value="12345678"/&gt;
       &lt;param name="CommunityString" value="public"/&gt;
       &lt;param name="ForwardStackTraceWithTrap" value="true"/&gt;
       &lt;param name="Threshold" value="DEBUG"/&gt;
       &lt;param name="ApplicationTrapOID" value="1.3.6.1.4.1.24.12.10.22.64"/&gt;
       &lt;layout class="org.apache.log4j.PatternLayout"&gt;
               &lt;param name="ConversionPattern" value="%d,%p,[%t],[%c],%m%n"/&gt;
       &lt;/layout&gt;
   &lt;/appender&gt;
</pre></code>
<br>
To configure the appender to use a delimited conversion pattern, to allow multiple VarBinds, you would
drop the parameter <xmp><param name="ApplicationTrapOID" value="1.3.6.1.4.1.24.12.10.22.64"/></xmp> from the
configuration, and change the Layout class to {@link org.apache.log4j.ext.SnmpDelimitedConversionPatternLayout}.
You would then need to set the appropriate parameters of the Layout class.  See
{@link SnmpDelimitedConversionPatternLayout} for an explanation of using multiple VarBinds.<br>
<pre><code>
   &lt;appender name="TRAP_LOG" class="org.apache.log4j.ext.SNMPTrapAppender"&gt;
       &lt;param name="ImplementationClassName" value="org.apache.log4j.ext.WengsoftSNMPTrapSender"/&gt;
       &lt;param name="ManagementHost" value="127.0.0.1"/&gt;
       &lt;param name="ManagementHostTrapListenPort" value="162"/&gt;
       &lt;param name="EnterpriseOID" value="1.3.6.1.4.1.24.0"/&gt;
       &lt;param name="LocalIPAddress" value="127.0.0.1"/&gt;
       &lt;param name="LocalTrapSendPort" value="161"/&gt;
       &lt;param name="GenericTrapType" value="6"/&gt;
       &lt;param name="SpecificTrapType" value="12345678"/&gt;
       &lt;param name="CommunityString" value="public"/&gt;
       &lt;param name="ForwardStackTraceWithTrap" value="true"/&gt;
       &lt;param name="Threshold" value="DEBUG"/&gt;
       &lt;layout class="org.apache.log4j.ext.SnmpDelimitedConversionPatternLayout"&gt;
             &lt;param name="ValuePairDelim" value="/"/&gt;
             &lt;param name="VarDelim" value=";"/&gt;
             &lt;param name="ConversionPattern" value="%p;1.3.6.1.4.1.24.100.1/%m;1.3.6.1.4.1.24.100.2/%C{1};1.3.6.1.4.1.24.100.3" /&gt;
       &lt;/layout&gt;
   &lt;/appender&gt;
</pre></code>
<p>
Here's a sample of what you would need in a properties configuration file to
configure this appender:
<br><br>
          log4j.appender.TRAP_LOG=org.apache.log4j.ext.SNMPTrapAppender<br>
          log4j.appender.TRAP_LOG.ImplementationClassName=org.apache.log4j.ext.JoeSNMPTrapSender<br>
          log4j.appender.TRAP_LOG.ManagementHost=127.0.0.1<br>
          log4j.appender.TRAP_LOG.ManagementHostTrapListenPort=162<br>
          log4j.appender.TRAP_LOG.EnterpriseOID=1.3.6.1.4.1.24.0<br>
          log4j.appender.TRAP_LOG.LocalIPAddress=127.0.0.1<br>
          log4j.appender.TRAP_LOG.LocalTrapSendPort=161<br>
          log4j.appender.TRAP_LOG.GenericTrapType=6<br>
          log4j.appender.TRAP_LOG.SpecificTrapType=12345678<br>
          log4j.appender.TRAP_LOG.ApplicationTrapOID=1.3.6.1.4.1.24.12.10.22.64<br>
          log4j.appender.TRAP_LOG.CommunityString=public<br>
          log4j.appender.TRAP_LOG.ForwardStackTraceWithTrap=true<br>
          log4j.appender.TRAP_LOG.Threshold=DEBUG<br>
          log4j.appender.TRAP_LOG.layout=org.apache.log4j.PatternLayout<br>
          log4j.appender.TRAP_LOG.layout.ConversionPattern=%d,%p,[%t],[%c],%m%n<br>
<br>
Here's an example using the properties file format the uses the delimited conversion pattern
technique to allow multiple VarBinds:
<br><br>
          log4j.appender.TRAP_LOG=org.apache.log4j.ext.SNMPTrapAppender<br>
          log4j.appender.TRAP_LOG.ImplementationClassName=org.apache.log4j.ext.JoeSNMPTrapSender<br>
          log4j.appender.TRAP_LOG.ManagementHost=127.0.0.1<br>
          log4j.appender.TRAP_LOG.ManagementHostTrapListenPort=162<br>
          log4j.appender.TRAP_LOG.EnterpriseOID=1.3.6.1.4.1.24.0<br>
          log4j.appender.TRAP_LOG.LocalIPAddress=127.0.0.1<br>
          log4j.appender.TRAP_LOG.LocalTrapSendPort=161<br>
          log4j.appender.TRAP_LOG.GenericTrapType=6<br>
          log4j.appender.TRAP_LOG.SpecificTrapType=12345678<br>
          log4j.appender.TRAP_LOG.CommunityString=public<br>
          log4j.appender.TRAP_LOG.ForwardStackTraceWithTrap=true<br>
          log4j.appender.TRAP_LOG.Threshold=DEBUG<br>
          log4j.appender.TRAP_LOG.layout=org.apache.log4j.ext.SnmpDelimitedConversionPatternLayout<br>
          log4j.appender.TRAP_LOG.layout.ValuePairDelim=/<br>
          log4j.appender.TRAP_LOG.layout.VarDelim=;<br>
          log4j.appender.TRAP_LOG.layout.ConversionPattern=
                 %p;1.3.6.1.4.1.24.100.1/%m;1.3.6.1.4.1.24.100.2/%C{1};1.3.6.1.4.1.24.100.3<br>
<p>
<b>This software is based on the log4j software provided by the Apache Jakrta log4j project.  This
software is released under a version of the Apache license version 1.1 -- please see the LICENSE.TXT
file included with this distribution for details.</b>
<p>
Version 1.2.9.1<br>
2001-09-29<br>
changes ---

2001-10-03: mwm : made changes needed to support v.1.1.1 of the SNMPTrapSenderFacade<br>

2002-09-02: mwm : changed to be compatible with Log4J v. 1.2.x<br>

2002-10-03: mwm : Made changes, mainly in #append, to deal with the
new SnmpTrapSenderFacade interface architecture.<br>

2002-10-15: mwm: Included the SysUpTimeResolver contributed by Thomas Muller.<br>

2002-10-15: mwm : changed the sysUpTime value to a long, to cope with the SysUpTimeResolver mechanism.<br>

2002-12-10: mwm : minor tweaks and prettying up of code.<br>

2003-03-21: mwm : incorporated the first cut of Matt Monks's code to use a delimited conversion pattern
string to allow mutliple VarBinds to be attached to the trap PDU.<br>

2003-03-22: mwm : after reading Ceki's fine new book on Log4J, made several changes (for example, to
the implementation of #close) to correct deficiencies in this class as an implementation of
AppenderSkeleton.  Also improved/added handling of the stack trace of the Throwable associated with
the LoggingEvent, again inspired by Ceki's example in the new book.<br>

2003-03-23: mwm : building on Matt Monks's ideas, added the SnmpDelimitedConversionPatternLayout, and used
it to refactor the handling of the delimited conversion pattern case.<br>

2003-05-24: mwm : minor changes to accomodate the change in the SnmpTrapSenderFacade interface, and added
two new properties.<br>

2003-07-05: mwm : some improvement in the exception handling of #loadImplementationClass<br>

Authors:
Mark Masterson (<a href="mailto:m.masterson@computer.org">m.masterson@computer.org</a>),
<br>Thomas Muller (<a href="mailto:ttm@online.no">ttm@online.no</a>)
<br>Matt Monks (<a href="mailto:Matthew.Monks@netdecisions.com">Matthew.Monks@netdecisions.com</a>)
