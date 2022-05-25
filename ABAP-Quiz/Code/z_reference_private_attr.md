What is the result of executing (F9) below report?

1. Batman Spiderman
2. Batman Batman
3. Short dump trying to change private attribute of a class from outside

```ABAP
REPORT z_reference_private_attr.

CLASS lcl_test_ref DEFINITION.
  PUBLIC SECTION.
    METHODS:
      get_name RETURNING VALUE(r_result) TYPE REF TO string,
      set_name IMPORTING i_name TYPE string.
  PRIVATE SECTION.
    DATA:
      name TYPE string.
ENDCLASS.

CLASS lcl_test_ref IMPLEMENTATION.
  METHOD get_name.
    r_result = REF #( me->name ).
  ENDMETHOD.

  METHOD set_name.
    me->name = i_name.
  ENDMETHOD.
ENDCLASS.

START-OF-SELECTION.
  DATA:
    lr_name TYPE REF TO string.

  DATA(lo_test) = NEW lcl_test_ref( ).

  lo_test->set_name( 'Batman' ).
  cl_demo_output=>write( lo_test->get_name( )->* ).

  lr_name = lo_test->get_name( ).
  lr_name->* = 'Spiderman'.

  cl_demo_output=>write( lo_test->get_name( )->* ).

  cl_demo_output=>display( ).
```
