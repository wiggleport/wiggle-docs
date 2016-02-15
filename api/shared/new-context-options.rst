.. highlight:: json

A string containing a JSON object with options for the new context. All values are optional. Default::

	{
		"shared": true,
		"huh": [1, 2, true, false, null, "nope"]
	}

@test
	``{ "attr": 1, "floobop": null }``

shared
	optional? Look at :ref:`constructor-test`.
