
# Goals

- A store defined at @modules\Organization\Program\api\store.ts
- It has a member names "yellowFilter"
- Whenever postModel updates (a value is set or unset), I need to update yellowFilter
- yellowFilter will store postModel is original an filter model and yellowFilter just stores the current state of the filter in a way so that I can display it to user in user friendly way when the data type of a given field is other than string.
- when filter field is a number/string value from a dropdown, I need to set the Value part of dropdown "Option"  in yellowFilter[FieldName].Value
- sometimes I am also want to custmize the FitlerModel<T>.FilterLabel to a more friend way rather than raw field name, e.g., UserId to User
- When in a yellowFilter if "Parent" to some other fields gets reset, its childrend fields also need to reset (useful for cascading dropdown)
- For boolean field, I will show it as "Yes" and "No"
- Show me how I can do it in generic way for every store, because every store will have a postModel (original filter model) and yellowFilter (human friendly filter model)

- KEEP PERFORMANCE AND RE-RENDERING IN MIND.

# Important

