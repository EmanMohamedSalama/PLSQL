# PLSQL
Create triggers / sequences pairs using dynamic sql to put them for all the tables dynamically

DECLARE
CURSOR DATA_CURSOR IS
       SELECT c.table_name, cl.column_name
       FROM user_constraints c , user_cons_columns cl ,user_objects j, user_tab_columns ta
       WHERE c.constraint_type = 'P'
       AND c.constraint_name = cl.constraint_name
       AND cl.table_name =c.table_name
       AND cl.table_name =ta.table_name
       AND cl.table_name = j.object_name
       AND cl.column_name= ta.column_name
        AND object_type='TABLE'
       AND data_type = 'NUMBER';
           
BEGIN
      FOR DATA_RECORD in DATA_CURSOR LOOP
        execute immediate 'CREATE SEQUENCE '||data_record.table_name||'_SEQ
  START WITH 600
  MAXVALUE 999999999999999999999999999
  MINVALUE 1
  NOCYCLE
  CACHE 20
  NOORDER ';
  
 execute immediate 'CREATE OR REPLACE TRIGGER '||data_record.table_name||'_TRG
 BEFORE INSERT OR UPDATE
 ON HR.'||data_record.table_name||'
 FOR EACH ROW
 BEGIN
  :new.'||data_record.column_name||' := '||data_record.table_name|| '_SEQ.nextval;
 END ;';

 END LOOP;
 
end;   

select * from user_errors;
