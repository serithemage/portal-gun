.. _portal_spec:

====================
Portal Specification
====================

.. code-block:: json

	{
		"spot_instance": {
			"instance_type": "string (required)", 
			"image_id": "string (required)", 
			"key_pair_name": "string (required)", 
			"identity_file": "string (required)", 
			"security_group_id": "string (required)", 
			"availability_zone": "string (required)", 
			"subnet_id": "string (optional)", 
			"ebs_optimized": "boolean (optional)", 
			"remote_group": "string (required)", 
			"remote_user": "string (required)", 
			"python_virtual_env": "string (optional)", 
			"extra_python_packages": [
				"string (optional)"
			]
		}, 
		"spot_fleet": {
			"iam_fleet_role": "string (required)"
		}, 
		"persistent_volumes": [
			{
				"volume_id": "string (required)", 
				"device": "string (required)", 
				"mount_point": "string (required)"
			}
		], 
		"channels": [
			{
				"direction": "string (required)", 
				"local_path": "string (required)", 
				"remote_path": "string (required)", 
				"recursive": "boolean (optional)", 
				"delay": "float (optional)"
			}
		]
	}

Draft portal specification as the one above can be created using :ref:`init <portal_cmd_init>` command.

Schema
======

**spot_instance**
^^^^^^^^^^^^^^^^^

	*Type: object. Required.*

	Specification of a Spot Instance.

spot_instance . **instance_type**
"""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Type of AWS EC2 Instance to be requested.

	Detailed description of available types can be found `here <https://aws.amazon.com/ec2/instance-types/>`_.

spot_instance . **image_id**
""""""""""""""""""""""""""""

	*Type: string. Required.*

	Id of `Amazon Machine Images (AMI) <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html>`_ to be used to launch the Instance.

	For information about Deep Learning AMIa see :ref:`bellow <deep_learning_amis>`.

spot_instance . **key_pair_name**
"""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Name of `AWS EC2 Key Pair <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html>`_ to be used to connect to the Instance. The Key Pair should match the identity file.

spot_instance . **identity_file**
"""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Location of identity file (private key) to be used for authentication when connecting to the Instance. This file is generated by AWS upon creation of a new Key Pair. The identity file should match the Key Pair.

spot_instance . **security_group_id**
"""""""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Id of `AWS EC2 Security Group <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html>`_ to be associated with the Instance. Security Group controls which inbound and outbound traffic is allowed for the Instance. Make sure to at least allow inbound ssh traffic (port 22).

spot_instance . **availability_zone**
"""""""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Name of `AWS Availability Zone <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html>`_ in which to launch the Instance. Availability Zone has to be within the Region, specified in the :ref:`application config <config>`.

	Note that Spot Instance prices might differ between Availability Zones.

spot_instance . **subnet_id**
"""""""""""""""""""""""""""""

	*Type: string. Optional.*

	Id of `Subnet <https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html>`_ to be used for the Instance. If not specified, default Subnet of the Availability Zone is used.

spot_instance . **ebs_optimized**
"""""""""""""""""""""""""""""""""

	*Type: boolean. Optional.*

	Enable/disable `EBS Optimization <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html>`_. An EBS–optimized instance uses an optimized configuration stack and provides additional, dedicated capacity for EBS I/O.

spot_instance . **remote_group**
""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Default AMI user group. For images based on Ubuntu in most cases the group will be *ubuntu*. If in doubt, check AMI usage instructions.

spot_instance . **remote_user**
"""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Default AMI username. For images based on Ubuntu in most cases the username will be *ubuntu*. If in doubt, check AMI usage instructions.

spot_instance . **python_virtual_env**
""""""""""""""""""""""""""""""""""""""

	*Type: string. Optional.*

	Default Python virtual environment to be used to install extra Python packages. Should be specified when *spot_instance.extra_python_packages* is specified.

spot_instance . **extra_python_packages**
"""""""""""""""""""""""""""""""""""""""""

	*Type: array of strings. Optional.*

	Extra Python packages to be installed in the default Python virtual environment.

**spot_fleet**
^^^^^^^^^^^^^^

	*Type: object. Required.*

	Specification of a Spot Instance Fleet.

spot_fleet . **iam_fleet_role**
"""""""""""""""""""""""""""""""

	*Type: string. Required.*

	IAM role that grants the Spot Fleet permission to terminate Spot Instances on your behalf when you cancel its Spot Fleet request. For instance:

	*arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role*

	where "123456789012" should be replaced by your AWS Account Id which can be found in `AWS Console <https://console.aws.amazon.com/billing/home?#/account>`_.

.. _portal_spec_volumes:

**persistent_volumes**
^^^^^^^^^^^^^^^^^^^^^^

	*Type: array of objects. Required.*

	Specifications of EBS volumes to be attached. Use :ref:`volume <volume_cmd>` group of commands to manage and list volumes.

	**Note:** to be able to attach EBS Volumes to an Instance, they should be in the same Availability Zone.

persistent_volumes[] . **volume_id**
""""""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Id of EBS volume to be attached to the Instance.

persistent_volumes[] . **device**
"""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Name of device to represent the attached volume. For example, ``/dev/xvdf``. See `documentation <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/block-device-mapping-concepts.html?icmpid=docs_ec2_console>`_ for details.

persistent_volumes[] . **mount_point**
""""""""""""""""""""""""""""""""""""""

	*Type: string. Required.*

	Mounting point within the Instance file system, where device representing the volume should be mounted. For example, ``/home/ubuntu/workspace`` (assuming that AMI username is *ubuntu*).

.. _portal_spec_channels:

**channels**
^^^^^^^^^^^^

	*Type: array of objects. Required.*

	Specifications of file synchronization channels.


channels[] . **direction**
""""""""""""""""""""""""""

	*Type: string. Required.*

	Direction of file transfer. Expected values are "*in*" and "*out*". Inbound channel transfers files from the remote Instance to the local machine. Outbound channel transfers files from the local machine to the remote Instance. 

channels[] . **local_path**
"""""""""""""""""""""""""""

	*Type: string. Required.*

	Local path to be used in synchronization. Note that synchronization is done via ``rsync``, therefore, similar rules regarding the trailing slash (/) in the source path are applied (see :ref:`excerpt <rsync_help>` of rsync help for details).

channels[] . **remote_path**
""""""""""""""""""""""""""""

	*Type: string. Required.*

	Remote path to be used in synchronization. Note that synchronization is done via ``rsync``, therefore, similar rules regarding the trailing slash (/) in the source path are applied (see :ref:`excerpt <rsync_help>` of rsync help for details).

channels[] . **recursive**
""""""""""""""""""""""""""

	*Type: boolean. Optional.*

	Enable/disable recursive synchronization. Disabled by default.

channels[] . **delay**
""""""""""""""""""""""

	*Type: float. Optional.*

	Delay between two consecutive synchronization attempts. Defaults to 1 second.

----

Additional Details
==================

.. _deep_learning_amis:

Deep Learning AMIs
^^^^^^^^^^^^^^^^^^

`Amazon Machine Images (AMI) <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html>`_ are used to create virtual machines within the AWS EC2. They capture the exact state of software environment: operating system, libraries, applications, etc. One can think of them as templates. Pre-configured AMIs can be found in `AWS Marketplace <https://aws.amazon.com/marketplace/>`_. Some of them are free to use, others have per hour license price depending on the set of pre-installed software. EC2 users can also created their own Images.

`Deep Learning AMIs <https://aws.amazon.com/machine-learning/amis/>`_ - is a group of AMIs created by Amazon specifically for deep learning applications. They come pre-installed with open-source deep learning frameworks including TensorFlow, Apache MXNet, PyTorch, Chainer, Microsoft Cognitive Toolkit, Caffe, Caffe2, Theano, and Keras, optimized for high performance on Amazon EC2 instances. These AMIs are free to use, you only pay for the AWS resources needed to store and run your applications. Official documentation, guides and tutorials can be found `here <https://docs.aws.amazon.com/dlami/latest/devguide/what-is-dlami.html>`_. 

There are several different flavors of Deep Learning AMIs. Check the `guide <https://docs.aws.amazon.com/dlami/latest/devguide/options.html>`_ to know the difference between them.

In order to instruct Portal Gun to use one of the Deep Learning AMIs to create an AWS Instance you need to know its **ID**:

1. Go to `AWS Marketplace <https://aws.amazon.com/marketplace>`_ and search for *"deep learning ami"*;
2. Pick an image from the search results, e.g. *Deep Learning Base AMI (Ubuntu)*;
3. On the AMI's page click **Continue to Subscribe** button;
4. On the opened page select **Manual Launch** tab;
5. In the **Launch** section you will see AMI IDs for different regions.

.. _rsync_help:

Rsync Help on Trailing Slash
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

*An excerpt of* ``man rsync``:

Recursively transfer all files from the directory src/bar on the machine foo into the /data/tmp/bar directory on the local machine::

	$ rsync foo:src/bar /data/tmp

A trailing slash on the source changes this behavior to avoid creating an additional directory level at the destination::

	$ rsync foo:src/bar/ /data/tmp

You can think of a trailing / on a source as meaning "copy the contents of this directory" as opposed to "copy the directory by name", but in both cases the attributes of the containing directory are transferred to the containing directory on the  destination. In other words, each of the following commands copies the files in the same way, including their setting of the attributes of /dest/foo::

	$ rsync /src/foo /dest
	$ rsync /src/foo/ /dest/foo
