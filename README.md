# aut-num
Create an AUT-NUM form for AFRINIC

# GIT
```
git checkout develop
```

# SETUP VUE
```
cd aut-num/form
npm install
npm run serve
```
# VUE is now running on http://localhost:8080/


# FEATURE
- Used vuetify 2+
- Used regex for validations
- Fully Accessible (Can used keyboard only to fill up the form)
- Darkmode
- error messages
- button disable if validation is not good.
- reset form button
- reset validation button

## Our backend developer just needs to know what is the data structure that you will provide when you call his API, he will do the rest.
After submitting the form, you will see a success dialog box. check the object on your the console before you click button 'continue' or check formVal data in Vue extension that will be sent to the backend developer.
data structure:

```diff
+formVal: {
     aut_num: "",
      as_name: "",
      descr: "",
      memberof: "",
      status: "",
      notify: "",
      changed: "",
      tech_c: "",
      org: "",
      remarks: "",
      admin_c: "",
      mnt_irt: "",
      mnt_by: "",
      mnt_lower: "",
      mnt_routes: {
        mnt_name: "",
        ip_address: [
          {
            ip: "",
            prefix: "ANY"
          }
        ]
      },
      default: {
        to: "",
        action: "",
        networks: ""
      },
      mp_default: {
        to: "",
        action: "",
        networks: ""
      },
      import: {
        protocol_1: "",
        import_peering: [
          {
            peering: "",
            action: "",
            count: 1
          }
        ],
        annonce: ""
      },
      export: {
        protocol_1: "",
        export_peering: [
          {
            peering: "",
            action: "",
            count: 1
          }
        ],
        annonce: ""
      },
      mp_import: {
        protocol_1: "",
        afi: "",
        mp_import_peering: [
          {
            peering: "",
            action: "",
            count: 1
          }
        ],
        filter: "",
        importexpression_filter: "",
        importexpression: ""
      },
      mp_export: {
        protocol_1: "",
        afi: "",
        mp_export_peering: [
          {
            peering: "",
            action: "",
            count: 1
          }
        ],
        filter: ""
      }
+    },
```

## Our PM thinks that 2 hours seems enough to have this form. Please tell us how much time you spent on it?

I spent around ~ 5 hours (need to understand routing policy first and also perform complex regex)

# further improvement
- Could be fully responsive
- UI can be improved
- more validations fore mt-routes, mp_export, export etc...
- mixins for repeated functions
- repeated field can be broken into components(just need to pass props - two way binding)


# REFERENCE
- https://vuetifyjs.com/en/ (vuetify)
- http://www.irr.net/docs/rfc2622.txt (learn routing policy)
- https://regex101.com/ (regex validation)