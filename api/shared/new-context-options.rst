.. highlight:: json
.. role:: json(code)
	:language: json

A string containing a JSON object with options for the new context. All values are optional. Default::

	{
		"shared": true,
		"huh": [1, 2, true, false, null, "nope"]
	}

@test
	:json:`[1, 2, true, false, null, "nope"]`

shared
	optional?
