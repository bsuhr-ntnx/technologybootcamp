.. _lab_manage_workloads:

------------------------
Managing Workloads Lab
------------------------

Overview
++++++++

Now that you have a couple VMs deployed, let’s have some fun and explore some of the VM management tasks with AHV (power actions, searching, cloning, and migrating).

Workload Management
+++++++++++++++++++

Power Actions and Console Access
................................

Explore VM power actions and console access.

#. In **Prism Element > VM > Table**, use the search bar to locate the Linux VM you created in the previous exercise (*Initials*-**Linux_VM**).

   Note that the Power State column for that VM shows a red dot, indicating that the VM is powered off.

#. Select the VM, then click **Power On**.

#. Select the VM, then click **Launch Console**.

   The console window provides 4 actions: Mount ISO, CTRL-ALT-DEL, Take Screen Capture, and Power.

   .. figure:: images/manage_workloads_01.png

   .. note::

     In ESX:

     - The steps in this exercise could also be done from Prism while using an ESXi cluster that has its VMware vCenter instance registered to Prism.

     .. figure:: images/manage_workloads_06.png

Cloning VMs
...........

#. In **Prism Element > VM > Table**, select your *Initials*-**Linux_VM** VM.

#. Click **Clone** from the **Actions** list.

#. Fill out the following fields and click **Save**:

   - **Number of Clones** - 2
   - **Prefix Name**  - *Initials*-Linux-Clone
   - **Starting Index Number** - 1

   .. figure:: images/manage_workloads_02.png

#. Leave them **Powered Off**.

   Both Nutanix snapshots and clones use a `redirect-on-write <https://nutanixbible.com/#anchor-book-of-acropolis-snapshots-and-clones>`_ algorithm to quickly and efficiently create copies of VMs as a metadata operation.

Migrating a VM Between Hosts
............................

VM live migration is a critical feature for any virtualized environment, allowing VMs to move seamlessly across hosts within a cluster to enable infrastructure maintenance or performance balancing.

#. In **Prism Element > VM > Table**, select your *Initials*-**Linux_VM** VM.

   You should see that it has no entry in the **Host** column when it is powered off.

   .. figure:: images/manage_workloads_03.png

#. Select the **Powered On** VM, then click **Migrate**.

   You can either choose one of the other hosts in the cluster as a migration target for the VM, or accept the default and let AHV automatically select a location.

#. Click **Migrate** to finalize the action.

   When the task completes, verify that your VM host location has changed from the host recorded above to the new location you selected.

   .. figure:: images/manage_workloads_04.png

Configuring Affinity Policies
.............................

#. In **Prism Element > VM > Table**, select your *Initials*-**Linux_VM** VM.

#. Select a **Powered Off** VM, then click **Update** and **+ Set Affinity**.

#. Select two **Hosts** to which the VM can have affinity, and click **Save** and **Save** to finish.

   .. note:: We select more then one host so the VM has a place to migrate too in the event of a Node failure.

#. Power on the VM, and verify it is on one of the **Hosts** you selected in the affinity policy.

#. Select the VM, then click **Migrate**.

   You should see the following message:

   - This VM has host affinity with 2 out of the 4 available hosts. It can only be migrated to those hosts.

#. Click **Migrate**.

   You should see that the VM has moved to the other host.

VM-to-Host affinity rules are commonly used to map VMs to certain hosts for performance or licensing reasons. AHV can also create VM-to-VM anti-affinity rules, commonly used for highly available applications where you need to ensure multiple instances of an application do not run on the same node.

High Availability & Dynamic Scheduling
......................................

Unlike ESXi, high availability is enabled by default for AHV and will restart VMs in a best-effort manner in the event of a host failure. Additional configuration can set resource reservations to ensure there is capacity during an HA event.

.. note::

   To enable memory reservation, select **Enable HA Reservation** under :fa:`cog` **> Manage VM High Availability**.

   As memory is already limited on the shared cluster resources, please do NOT enable HA memory reservations.

With the **Acropolis Dynamic Scheduler** service, AHV performs intelligent initial placement of VMs and can dynamically migrate VMs to other hosts within the cluster to optimize workload performance. This is done "out of the box" without additional configuration.

A benefit of a Nutanix AHV solution is being able to make VM placement decisions not based solely on CPU/memory congestion avoidance, but also based on storage performance.

See `here <https://nutanixbible.com/#anchor-book-of-acropolis-dynamic-scheduler>`_ for additional details about the **Acropolis Dynamic Scheduler**.

Prism Search
............

The Prism search function makes it easier to identify problems or find feature documentation in Prism Central. Use Prism Central’s search capabilities by typing a few search queries to see how easy this can make the tasks above.


Suggestions:

- vm cpu > 1
- vm mem > 2
- vm iops
- create vm
- powered on
- powered on cpu = 8

#. In **Prism Central >** :fa:`search`.

- Note the result types: Entity, Alerts, and Help.
- Click the star icon to save a search.

.. note::

  The search hot key (a slash mark, or /) can be used from anywhere in the Prism Central UI to bring up the search function.


Role Based Access Control (RBAC)
................................

Role based access control offers the ability to apply fine grained permissions to a user or group of users to precisely control the actions that they are allowed to take. Prism Central allows RBAC on many functions such as VM actions, reporting, Calm, self-service and others. In this lab example we will focus on the VM based RBAC controls.

#. Expand burger menu on left and select Roles from the Administration menu.

   .. figure:: images/role_menu.png

#. The Roles page shows the existing roles that are available on the Prism Central. These should all be System created roles that can be used, modified or cloned.

   .. figure:: images/role_table.png

#. Click the Create Role button at the top to begin process to create a new custom role.

#. Enter Help Desk in the role name field and you can also enter a summary in the description field if desired.

   .. figure:: images/create_role.png

#. Expand the VM entities field on the create role screen and click on the Change hyperlink on the Set custom permissions line.

#. From the list of customer VM permissions select the following items:

- access Console VM
- Clone VM
- View VM

   .. figure:: images/perms_list.png

Click save and and then click save on the Role creation page.

#. Now back on the Roles page, select the newly created role and from the actions menu choose Manage Assignment.

   .. figure:: images/role_assign.png

#. From here we will now assign the users/groups we want to entitle with these permissions and the entities to which they will apply.

   .. figure:: images/assign_role.png

#. Click on the New Users button to enter in users IDs or groups. Enter in the filed the devuser01 ID which is precreated in the labs AD domain.

   .. figure:: images/role_assign_user.png

#. Next click the new entities button to assign this role’s permissions over these entities.

   .. figure:: images/entity1.png

#. You can either assign the roles to a list of individual VMs or to category(s). Begin typing in Prism or another VM name into the field after select the VM option. This will assign this role to an VM that already exists on this cluster.

   .. figure:: images/entity2.png

#. You enter additional VMs or categories if desired or click Save to complete.

#. The role is now created and assigned and if you want to test you can logout as admin and log back into Prism Central with the assigned user ID and you will only have the assigned privileges to the VM(s) that were assigned.


Takeaways
+++++++++

- In this lab you got to experience first hand how AHV provides a complete set of tools and actions that can be done manage the VMs in the cluster.
- Also provided an overview of how easy it is to assign permissions and control access to resources within Prism Central. 
- It is possible to register an ESXI cluster to Prism and be able to perform some of the basic VM management tasks right from Prism as well.
