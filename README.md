activeadmin_ajax-forms
======================

## Installation

In your *Gemfile*:

```ruby
gem 'ajax_forms', github: 'Captive-Studio/activeadmin-ajax_forms'
```
  
Then run `bundle install`.

In your active_admin.js, include the js file:

```javascript
//= require ajax_forms/application
```

## Usage

In this exemple, we will create an `House` with one `Door`.
We want to add Door if not exist when we create the object House.

in the file `app/admin/door.rb`
```ruby
ajax_forms quick_new_partial: 'door',
           find_conditions: {
             m: 'or',
             per:30,
             find_fields: [:name_contains, :height_contains]
           }
```

in the file `app/admin/house.rb`
```erb
form do
<%= semantic_form_for [:admin, @item], remote: (defined? @remote) ? true : false, url: @form_url, :builder => FormtasticBootstrap::FormBuilder do |f| %>

<div id="quick-add-errors"></div> <!-- Agregar para mostrar errores -->

# Poner la clase select2-autocomplete para que funcione el selector con select2
# Poner la clase ajax si se quiere hacer la búsqueda de los resultados en el selector mediante ajax
# En el atributo data se deben pasar las opciones:
#  - multiple (boolean): true (default) - indica si el selector es simple o multiple (aún no implementado)
#  - source: path a la función find donde se devuelve key y value a mostrar en el selector. @todo: modificar para que si no se pasa nada se tome la función por defecto de la gema. En caso contrario se puede sobreescribir por una propia
#  - modal: path del controller que renderiza el partial en el modal fancybox

<%= 
  f.input :item_id,
          label: false,
          as: :string,
          input_html: {
            class: 'select2-autocomplete ajax',
            data: {
              multiple: false,
              source: find_admin_items_path,
              modal: quick_new_admin_items_path
            }
          }
%>

# custom find method:
# add dependent condition:
# - selectorId: value of this selector id is passed to parameters
# - ransackFilter: filter strinc compatible with ransack gem. It will be applied with selectorId value when filter over select2 box
<%= 
  f.input :project_id,
          as: :string,
          input_html: {
            class: 'select2-autocomplete ajax',
            data: {
              multiple: false,
              source: find_projects_admin_invoices_path,
              modal: quick_new_admin_projects_path,
              dependent: {
                selectorId: 'invoice_partner_id',
                ransackFilter: :partner_id_eq
              }
            }
          }
%>

<%= end %>
```

## Required

- Select2
- Fancybox