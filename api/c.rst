C/C++
=====

The native interface to libwiggleport is C-callable, and therefore it's directly usable with C or C++ as well as many higher level languages that include a Foreign Function Interface library.


Contexts
--------

Your connection to libwiggleport itself.

.. c:type:: wigl_context

  Placeholder

.. c:function:: wigl_context* wigl_context_new(const char* json_options, char** error)

  Placeholder

.. c:function:: void wigl_context_unref(wigl_context* ctx)

  Placeholder


Package management
------------------

Packages generate models based on the attached hardware.
By default, libwiggleport loads a standard set of packages. Most programs
won't need the package management functions.

.. c:function:: bool wigl_package_load(wigl_context* ctx, const char* json_content, const char* json_fileset, char** error)

  Placeholder

.. c:function:: bool wigl_package_load_yaml(wigl_context* ctx, const char* yaml_content, const char* json_fileset, char** error)

  Placeholder

.. c:function:: bool wigl_package_load_file(wigl_context* ctx, const char* path, char** error)

  Placeholder

.. c:function:: const char* wigl_package_name(const wigl_context* ctx, int index)

  Placeholder

.. c:function:: const char* wigl_package_version(const wigl_context* ctx, const char* name)

  Placeholder

.. c:function:: const char* wigl_package_json(const wigl_context* ctx, const char* name)

  Placeholder

Enumerator
----------

The enumerator can find hardware models, and get hotplug notifications.

.. c:type:: wigl_enumerator

.. c:function:: wigl_enumerator* wigl_enumerator_new(wigl_context* ctx, const char* json_options, char** error)

.. c:function:: void wigl_enumerator_delete(wigl_enumerator* en)

.. c:function:: bool wigl_enumerator_next(wigl_enumerator* en, wigl_model** added, wigl_model** removed)

.. c:type:: wigl_enumerator_cb

.. c:function:: void wigl_enumerator_notify(wigl_enumerator* en, wigl_enumerator_cb* cb, void* cb_data)

Model
-----

The model is a data tree for interacting with hardware.

.. c:type:: wigl_model

.. c:function:: void wigl_model_unref(wigl_model* model)

.. c:function:: const char* wigl_model_json(const wigl_model* model)

Tuple
-----

A tuple is an observed set of values from the model.

.. c:type:: wigl_tuple

.. c:function:: wigl_tuple* wigl_tuple_new(const wigl_model* model, const char* json_refs, char** error)

.. c:function:: void wigl_tuple_delete(wigl_tuple* tuple)

.. c:function:: bool wigl_tuple_next(wigl_tuple* tuple, char** json_change_detail)

.. c:type:: wigl_tuple_cb

.. c:function:: void wigl_tuple_notify(wigl_tuple* tuple, wigl_tuple_cb* cb, void* cb_data)

.. c:function:: const char* wigl_tuple_json(const wigl_tuple* tuple)

.. c:function:: int64_t wigl_tuple_int(const wigl_tuple* tuple, int index, char** error)

.. c:function:: double wigl_tuple_number(const wigl_tuple* tuple, int index, char** error)

.. c:function:: const char* wigl_tuple_string(const wigl_tuple* tuple, int index, char** error)

Action
------

An action is a change that can be applied to the model.

.. c:type:: wigl_action

.. c:function:: wigl_action* wigl_action_new(const wigl_model* model, const char* json_dict, char** error)

.. c:function:: void wigl_action_delete(wigl_action* ac)

.. c:function:: bool wigl_action_set_int(wigl_action* ac, const char* name, int64_t value, char** error)

.. c:function:: bool wigl_action_set_number(wigl_action* ac, const char* name, double value, char** error)

.. c:function:: bool wigl_action_set_string(wigl_action* ac, const char* name, const char* value, char** error)

.. c:function:: wigl_schedule* wigl_action_schedule(wigl_action* ac, const char* json_options, char** error)

Schedule
--------

A Schedule is Wiggleport's representation of some planned events happening at a particular time across one or more streams.

.. c:type:: wigl_schedule

.. c:function:: void wigl_schedule_delete(wigl_schedule* sched)

.. c:function:: const char* wigl_schedule_json(const wigl_schedule* sched)

.. c:function:: bool wigl_schedule_has_finished(const wigl_schedule* sched)

.. c:function:: void wigl_schedule_wait(const wigl_schedule* sched)

.. c:type:: wigl_schedule_cb

.. c:function:: void wigl_schedule_notify(wigl_schedule* sched, wigl_schedule_cb* cb, void* cb_data)

Stream
------

Streams are Wiggleport's interface for real-time input and output.

.. c:type:: wigl_stream

.. c:function:: wigl_stream* wigl_stream_new(wigl_model* model, const char* json_options, char** error)

.. c:function:: void wigl_stream_delete(wigl_stream* st)

.. c:function:: const char* wigl_stream_json(const wigl_stream* st)

.. c:type:: wigl_stream_cb

.. c:function:: void wigl_stream_notify(wigl_stream *st, wigl_stream_cb* cb, void* cb_data)

.. c:function:: uint64_t wigl_stream_clock(const wigl_stream* st)

.. c:function:: wigl_schedule* wigl_stream_read(wigl_stream* st, uint8_t *buffer, size_t byte_count, uint64_t time_ref)

.. c:function:: wigl_schedule* wigl_stream_write(wigl_stream* st, const uint8_t *buffer, size_t byte_count, uint64_t time_ref)

