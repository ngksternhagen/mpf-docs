How to configure a classic single-ball trough
=============================================

This guide will show you how to configure MPF to use an older-style single
ball drain. This is the type of configuration that most (all?) single-ball
machines use.

The following diagram shows the type of layout this guide is for: (This is a
side view)

.. image:: /mechs/images/classic_single_ball.png

1. Add the drain switch
-----------------------

The first step is to add the drain switch to the ``switches:``
section of your machine config file.

::

    switches:
        s_drain:
            number: 01

Note that we configured this switches with number ``01``, but you should use the
actual switch number for your control system that the switch is connected to.
(See :doc:`/hardware/numbers` for instructions for each type of control system.)

2. Add the eject coil
---------------------

Next, create the entry in your ``coils:`` section for the drain eject
coil. Again, the name doesn't matter. We'll call it *c_drain_eject* and enter it
like this:

::

    coils:
        c_drain_eject:
            number: 03
            pulse_ms: 20

Again, the ``number:`` entry in your config will vary depending on your actual
hardware, and again, you can pick whatever name you want for your coil.

You'll also note that we went ahead and entered a ``pulse_ms:`` value of 20
which will override the default pulse times of 10ms. It's hard to say
at this point what values you'll actually need. You can always adjust
this at any time. You can play with the exact values in a bit once we
finish getting everything set up.

3. Add your "drain" ball device
-------------------------------

In MPF, anything that holds and releases a ball is a
:doc:`ball device </mechs/ball_devices/index>`. So in your ``ball_devices:``
section, create an entry called ``bd_drain:`` like this: (If you don't have that
section add it now.)

::

    ball_devices:
        bd_drain:

This means that you're creating a ball device called *bd_drain*.
We use the preface *bd_* to indicate that this is a ball device
which makes it easier when we're referencing them later. Then under
your ``bd_drain:`` entry, you'll start entering the
configuration settings for your drain ball device.

* Add ``ball_switches: s_drain`` which means this device will use the *s_drain*
  switch to know whether or not this device has a ball.
* Add ``eject_coil: c_drain_eject`` which is the name of the coil that will
  eject the ball from the drain.
* Add ``eject_targets: bd_plunger_lane`` which tells MPF that this ball device
  ejects its balls into the device called *bd_plunger_lane*. (We won't actually
  create the plunger device in this How To guide, but you need to have it, so
  see the :doc:`/mechs/plungers/index` documentation for full details since
  there are lots of different types of plungers.
* Add ``tags: drain, home, trough`` which tells MPF that balls entering this
  device mean that a ball has drained from the playfield, that it's ok to start
  a game with a ball here, and that this device is used to store unused balls.

Your drain device configuration should look now look like this:

::

    ball_devices:
        bd_drain:
            ball_switches: s_drain
            eject_coil: c_drain_eject
            eject_targets: bd_plunger_lane
            tags: drain, home, trough

4. Configure your virtual hardware to start with balls in the trough
--------------------------------------------------------------------

While we're talking about the trough, it's probably a good idea to configure
MPF so that when you start it in virtual mode (with no physical hardware) that
it starts with the trough full of balls. To do this, add a new section to your
config file called ``virtual_platform_start_active_switches:``. (Sorry this
entry name is hilariously long.) As its name implies,
*virtual_platform_start_active_switches:* lets you list the names of
switches that you want to start in the "active" state when you're
running MPF with the virtual platform interfaces.

The reason these only work with the virtual platforms is because if you're
running MPF while connected to a physical pinball machine, it doesn't
really make sense to tell MPF which switches are active since MPF can
read the actual switches from the physical machine. So you can add
this section to your config file, but MPF only reads this section when
you're running with one of the virtual hardware interfaces. To use it,
simply add the section along with a list of the switches you want to
start active. For example:

::

    virtual_platform_start_active_switches:
        s_drain

Here's the complete config
--------------------------

.. begin_mpfdoctest:config/config.yaml

::

    #config_version=4

    switches:
        s_drain:
            number: 01

    coils:
        c_drain_eject:
            number: 03
            pulse_ms: 20

    ball_devices:
        bd_drain:
            ball_switches: s_drain
            eject_coil: c_drain_eject
            eject_targets: bd_plunger_lane
            tags: drain, home, trough

        # bd_plunger is a placeholder just so the trough's eject_targets are valid
        bd_plunger_lane:
            tags: add_ball_live
            mechanical_eject: true

    virtual_platform_start_active_switches:
        s_drain

.. end_mpfdoctest
