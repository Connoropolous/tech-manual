# Services in Loomio
## What does a service look like?
- a file in app/services/name.rb
- it's a ruby module (well it should be but most are classes right now)
- it's a class only if it must hold state and be instantiated
- it has method names that try to map to controller actions if suitable
- parameters should be named rather than ordered. EG: def self.update(group:, params:, actor:)
- common method signatures:
  - create(group: group, actor: current_user)
  - update(group: group, params: params, actor: current_user)
  - destroy(group: group, actor: current_user)

## How does a service behave?

- it checks permissions for _action_ by _actor_ and raises AccessDenied if permission denied.
- returns boolean for restful actions: `true` if save was successful, `false` otherwise
- just to be clear: do not call `save!` or `update!` they raise error on validation which we don't want to do.
- you already have a reference to the subject model so don't need to return it for the restful actions.
