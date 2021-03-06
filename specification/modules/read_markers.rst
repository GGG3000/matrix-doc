.. Copyright 2018 New Vector Ltd
..
.. Licensed under the Apache License, Version 2.0 (the "License");
.. you may not use this file except in compliance with the License.
.. You may obtain a copy of the License at
..
..     http://www.apache.org/licenses/LICENSE-2.0
..
.. Unless required by applicable law or agreed to in writing, software
.. distributed under the License is distributed on an "AS IS" BASIS,
.. WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
.. See the License for the specific language governing permissions and
.. limitations under the License.

Fully read markers
==================

.. _module:read-markers:

The history for a given room may be split into three sections: messages the
user has read (or indicated they aren't interested in them), messages the user
might have read some but not others, and messages the user hasn't seen yet.
The "fully read marker" (also known as a "read marker") marks the last event
of the first section, whereas the user's read receipt marks the last event of
the second section.

Events
------
The user's fully read marker is kept as an event in the room's `account data`_.
The event may be read to determine the user's current fully read marker location
in the room, and just like other account data events the event will be pushed down
the event stream when updated.

The fully read marker is kept under an ``m.fully_read`` event. If the event does
not exist on the user's account data, the fully read marker should be considered
to be the user's read receipt location.

{{m_fully_read_event}}

Client behaviour
----------------
The client cannot update fully read markers by directly modifying the ``m.fully_read``
account data event. Instead, the client must make use of the read markers API
to change the values.

The read markers API can additionally update the user's read receipt (``m.read``)
location in the same operation as setting the fully read marker location. This is
because read receipts and read markers are commonly updated at the same time,
and therefore the client might wish to save an extra HTTP call. Providing an
``m.read`` location performs the same task as a request to ``/receipts/m.read/$event:example.org``.

{{read_markers_cs_http_api}}

Server behaviour
----------------
The server MUST prevent clients from setting ``m.fully_read`` directly in
room account data. The server must additionally ensure that it treats the
presence of ``m.read`` in the ``/read_markers`` request the same as how it
would for a request to ``/receipts/m.read/$event:example.org``.

Upon updating the ``m.fully_read`` event due to a request to ``/read_markers``,
the server MUST send the updated account data event through to the client via
the event stream (eg: ``/sync``), provided any applicable filters are also
satisfied.


.. _`account data`: #client-config
