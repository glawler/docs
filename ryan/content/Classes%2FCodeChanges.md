General

* There is one course per project.
* The project_head is the primary instructor
* Anybody with group_root permission is assumed to be a TA

1.  *Instructors and TA's are allowed to sudo to any student on the ops node*

 Instructors may sudo to a TA but not conversely.

__Code changes__:

 The instacct utility (described below) has a function which generates an include file
 for sudo which is copied to the ops node and installed as /usr/local/etc/sudoers.classes.
 The function is run either by explicit request on the command line, or as a side-effect
 when students are assigned to a class, and at the end of the semester when a class is wiped.

1.  *Experiment Permissions*

 When an experiment is created in the default group, only the
 student's home directory and /proj/<PID>/exp/<EID> are exported to the
 nodes in the experiment.

 If the experiment is created in a subgroup of the main project, normal
 export permissions already isolate students from others (except for those
 in the group). /proj directory exports are applied as above.

 The ssh public keys of the instructor and TA's are put into the
 root .ssh/authorized_keys file so that the instructors can log 
 into any node to grade the experiment (as class exercise) or
 debug it.

 __Code changes__: tmcd

1.  *Web Interface*

 Instructors and TA's are allowed to Freeze, Thaw and SU-as a student
 and edit a student's profile.  When an instructor ``removes`` a class
 account from the project, instead of actually removing it, it recycles the account.

 __Code changes__: 

 www/user_defs.php defines new routines InstructedBy() and CourseAcct(),
 referenced in delete_user.php3 moduserinfo.php3 showuser.php3 suuser.php3.
 There are perl versions of these in db/User.pm.in.

1.  *Recyclable student accounts*

 Student accounts are not created in the normal manner (create
 an account, apply to join an existing project) - instead:

 A stem is chosen for the project, say in the case of the
 project USC558L, sc558, and then a number of accounts are
 generated of the form sc558[a-z][a-z] as many are need
 to accommodate the students in the class.

 The instructor provides a list of email address, and
 one account is assigned per email address.

 At the end of the semester, the student accounts are wiped -
 all experiments headed by the student are terminated, all files
 underneath the students home directory are deleted, the passwords
 changed to something random, all public ssh keys and ssl certs
 recorded in the database are flushed and then randomly regenerated
 as in a new account.

 __Code changes__:

 The source for the instacct utility was added to the tree as

 testbed/account/instacct.in, manage_class.in
 
 The separate perl script manage_class gets installed on the ops node
 to invoke an sslxmlrpc call so that instructors may do almost anything 
 that a testbed adminstrator could do.

 The two commands reserved to admins are to set the stem for class names, and to generate
 a number of accounts in advance that are ready to assign - in the same state
 a previously used and wiped account as in.

 Since our funders require us to provide usage goals and metrics, we track
 the number of student accounts which were generated, (re-)assigned and wiped
 by means of a couple of additional tables in the database: 

 project_history and project_attributes.

1. *Other peculiarities of student accounts*

 Student accounts may not join other projects.

 A student may be taking more than one course and only have
 one (student) email address; we added a couple of warts to deal
 with this -

 There is an ancillary table in the database called email_aliases;
 and when the account is assigned the .forward is set to this     
 and the students email becomes e.g. sc558ab@users.isi.deterlab.net

 So, for all users, students or not, we require web login by uid only
 and not email address.

 __Code changes__:

 account/tbacct.in was changed so that when it generates a .forward
 file, the email_aliases entry preempts the usr_email in the users table.

 An unresolved bug is letting an instructor correct a students email, now that
 it is kept in a separate place.

