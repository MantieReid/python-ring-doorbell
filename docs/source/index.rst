=================================================
Python Ring Door Bell's documentation
=================================================

Python Ring Door Bell is a library written in Python 2.7/3x
that exposes the Ring.com devices as Python objects.


.. note::
    Ring.com does not provide an official API.
    The results of this project are merely from reverse engineering.

.. toctree::
   :maxdepth: 2
   :caption: Contents:

Installation
------------

.. code-block:: bash

    # Installing from PyPi
    $ pip install ring_doorbell

    # Installing latest development
    $ pip install \
        git+https://github.com/tchellomello/python-ring-doorbell@dev


Initializing your Ring object
-----------------------------

.. code-block:: python

    from ring_doorbell import Ring
    myring = Ring('foo@bar', 'secret')

    myring.is_connected
    True

Listing devices linked to your account
--------------------------------------

.. code-block:: python

    # All devices
    myring.devices
    {'chimes': [<RingChime: Downstairs>],
    'doorbells': [<RingDoorBell: Front Door>]}

    # All chimes
    myring.chimes
    [<RingChime: Downstairs>]

    # All door bells
    myring.doorbells
    [<RingDoorBell: Front Door>]

    # All stickup cams
    myring.stickup_cams
    [<RingStickUpCam: Driveway>]


Playing with the attributes
---------------------------
.. code-block:: python

    for dev in list(myring.stickup_cams + myring.chimes + myring.doorbells):

        # refresh data
        dev.update()

        print('Account ID: %s' % dev.account_id)
        print('Address:    %s' % dev.address)
        print('Family:     %s' % dev.family)
        print('ID:         %s' % dev.id)
        print('Name:       %s' % dev.name)
        print('Timezone:   %s' % dev.timezone)
        print('Wifi Name:  %s' % dev.wifi_name)
        print('Wifi RSSI:  %s' % dev.wifi_signal_strength)

        # setting dev volume
        print('Volume:     %s' % dev.volume)
        dev.volume = 5
        print('Volume:     %s' % dev.volume)

        # play dev test shound
        if dev.family == 'chimes'
            dev.test_sound


Showing door bell events
------------------------
.. code-block:: python

    for doorbell in myring.doorbells:

        # listing the last 15 events of any kind
        for event in doorbell.history(limit=15):
            print('ID:       %s' % event['id'])
            print('Kind:     %s' % event['kind'])
            print('Answered: %s' % event['answered'])
            print('When:     %s' % event['created_at'])
            print('--' * 50)

        # get a event list only the triggered by motion
        events = doorbell.history(kind='motion')


Downloading the last video triggered by ding
--------------------------------------------
.. code-block:: python

    doorbell = myring.doorbells[0]
    doorbell.recording_download(
        doorbell.history(limit=100, kind='ding')[0]['id'],
                         filename='/home/user/last_ding.mp4',
                         override=True)


Displaying the last video capture URL
-------------------------------------
.. code-block:: python

    print(doorbell.recording_url(doorbell.last_recording_id))
    'https://ring-transcoded-videos.s3.amazonaws.com/99999999.mp4?X-Amz-Expires=3600&X-Amz-Date=20170313T232537Z&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=TOKEN_SECRET/us-east-1/s3/aws4_request&X-Amz-SignedHeaders=host&X-Amz-Signature=secret'


Getting all of the event ID's
-----------------------------

.. code-block:: python

  print("getting the total number of videos in the ring account")
  
  # enters the username and the password from the config file
  myring = Ring(config.username, config.password)  # enters the username and the password from the config file
  
  # selects the first doorbell from the doorbell query lists.
  doorbell = myring.doorbells[0]
  
  # events is a list that will store  info from the history.
  events = []
  
  # a counter that will be used to count the number of videos
  counter = 0
  
  # get the information about the last 100 videos taken.
  history = doorbell.history(limit=100)
  
  # keeps doing it until it gets all of the videos info from the ring account.
  while (len(history) > 0):
  
    # info from history is added to events.
    events += history
  
    # tells us the total amount of videos in the account.
    counter += len(history)
  
    # gets 100 videos that are older than  the last video listed in the list.
    history = doorbell.history(older_than=history[-1]['id'])
  
    # prints out the total amount of videos
  print("total amount of videos is " + str(counter))
  
  # this will hold all of the download urls.
  downloasdurl = []
  
  # the list that will hold the video ID's
  eventidlist = []
  
  # enters the password and username for ring.
  myring = Ring(config.username, config.password)
  
  # gets the first doorbell found in the ring list.
  doorbell = myring.doorbells[0]
  
  for doorbell in myring.doorbells:
  
    # listing the last 100 events of any kind
    for event in doorbell.history(limit=100):
  
      # appends the eventids to the eventidlist.
      eventidlist.append(event['id'])
  
      # prints the length of list id eventidlist
    print("the length of eventid list is " + str(len(eventidlist)))
  
    # prints out all of the items in the eventID list.
    print("eventidlist is " + str(eventidlist))
  
    # defines history to get all of the videos older than the last video listed in the list.
    history = doorbell.history(limit=100, older_than=eventidlist[-1])
  
    # defines history to get all of the videos older than the last video listed in the list.
    while (len(eventidlist) < counter):
      history = doorbell.history(limit=100, older_than=eventidlist[-1])
  
      for event in history:
  
        # adds the IDs to the list.
        eventidlist.append(event['id'])
  
        # removes any duplicates in the list.
        eventidlist = list(dict.fromkeys(eventidlist))
  
        # prints the length of the list
      print("the length of eventid list is " + str(len(eventidlist)))
  
      # prints what is in the list.
      print("event id list is " + str(eventidlist))



Developing
==========

.. autoclass:: ring_doorbell.Ring
    :members:
    :undoc-members:
    :show-inheritance:

.. autoclass:: ring_doorbell.generic.RingGeneric
    :members:
    :undoc-members:
    :show-inheritance:

.. autoclass:: ring_doorbell.chime.RingChime
    :members:
    :undoc-members:
    :show-inheritance:

.. autoclass:: ring_doorbell.doorbot.RingDoorBell
    :members:
    :undoc-members:
    :show-inheritance:
    :inherited-members:

.. autoclass:: ring_doorbell.stickup_cam.RingStickUpCam
    :members:
    :undoc-members:
    :show-inheritance:
    :inherited-members:


Credits && Thanks
-----------------

* This project was inspired and based on https://github.com/jeroenmoors/php-ring-api. Many thanks @jeroenmoors.
* A guy named MadBagger at Prism19 for his initial research (http://www.prism19.com/doorbot/second-pass-and-comm-reversing/)
* The creators of mitmproxy (https://mitmproxy.org/) great http and https traffic inspector
* @mfussenegger for his post on mitmproxy and virtualbox https://zignar.net/2015/12/31/sniffing-vbox-traffic-mitmproxy/
* To the project http://www.android-x86.org/ which allowed me to install Android on KVM.


Indices and tables
==================

* :ref:`genindex`
* :ref:`search`

.. _Python Ring DoorBell: https://github.com/tchellomello/python-ring-doorbell
