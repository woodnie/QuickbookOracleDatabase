### Concept {#concept}

**USER_SYNONYMS**

USER_SYNONYMS describes theprivate synonyms (synonyms owned by the current user). Its columns () are thesame as those in ALL_SYNONYMS.

About Dynamic Performance Views

Oracle contains a setof underlying views that are maintained by the database server and accessibleto the database administrator user SYS. These views are calleddynamicperformance views because they are continuously updated while a database isopen and in use, and their contents relate primarily to performance.

Although these viewsappear to be regular database tables, they are not. These views provide data oninternal disk structures and memory structures. You can select from theseviews, but you can never update or alter them.

**V$ Views**

The actual dynamic performance views are identified bythe prefix V_$. Public synonyms for these views have the prefixV$. Database administrators and other users should access only theV$objects, not theV_$ objects.

**USER_OBJECTS**

USER_OBJECTS describes all objects owned by the current user. Its columns are the sameas those in&quot;ALL_OBJECTS&quot;.

**About Static Data Dictionary Views**

Data dictionary tables are not directly accessible, but you can access information in them through data dictionary views. To list the data dictionary views available to you, query the view DICTIONARY.

Many data dictionary tables have three corresponding views:

·        An **ALL_view** displays all the information accessible to the current user, including information from the current user&amp;apos;s schema as well as information from objectsin other schemas, if the current user has access to those objects by way of grants of privileges or roles.

·        A **DBA_view** displays all relevant information in the entire database. DBA_ views are intended only for administrators. They can be accessed only by users with the SELECT ANY TABLE privilege. This privilege is assigned to the DBA role when the system is initially installed.

·        A **USER_view** displays all the information from the schema of the current user. Nospecial privileges are required to query these views.

The columns of the **ALL_, DBA_, and USER_ views** corresponding to a single data dictionary table are usually nearlyidentical. Therefore, these views are described in full only once in thischapter, at their first occurrence alphabetically, and are listed without fulldescriptions at their other occurrences.