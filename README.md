# School-Enrollment-Form-using-JasonPowerDB

<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Student Enrollment Form</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet"
              href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">
              <link rel="stylesheet" href="style.css" type="text/css"/>

        <script
        src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
        <script
        src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>
         <script src="http://login2explore.com/jpdb/resources/js/0.0.3/jpdb-commons.js"></script>
    </head>
    <body>
        <div>
            <h2>Student Enrollment Form</h2>
            <form id="stuForm" method="get">

                <div >
                    <label >Roll No:</label>
                    <input type="text"  id="rollno" onchange="getStudent()">
                </div>
                <div>
                    <label>Enter Full Name:</label>
                    <input type="text" id="fullName">
                </div>    
                <div >
                    <label >Class:</label>
                    <input type="text" id="class"
                           >
                </div>
                <div class="form-group">
                    <label >Birth Date:</label>
                    <input type="date" id="birthDate"
                            >
                </div>
                <div>
                    <label >Address:</label>
                    <input type="text" id="add"
                            >
                </div>
                <div>
                    <label >Enrollment Date:</label>
                    <input type="date" id="enrolDate"
                           >
                </div>
                <div>
                     <button type="button"  id="save" onclick="saveStudent()" disabled>Save </button>
                     <button type="button"  id="change" onclick="changeData()" disabled>Change </button>
                     <button type="button"  id="reset" onclick="resetForm()" disabled>Reset </button>
                </div>
               
            </form>
        </div>
        <script src="http://login2explore.com/jpdb/resources/js/0.0.3/jpdb-commons.js"></script>
        <script>
            var jpdbBaseURL="http://api.login2explore.com:5577";
            var jpdbIRL="/api/irl";
            var jpdbIML="/api/iml";
            var stuDBName="SCHOOL";
            var stuRelationName="STUDENT-TABLE";
            var connToken="90932305|-31949271145725399|90954053";
            
            $("#rollno").focus();
            
            function validateAndGetFormData() {
                var rollnoVar = $("#rollno").val();
                if (rollnoVar === "") {
                    alert("Roll Number is Required Value");
                    $("#rollno").focus();
                    return "";
                }
                var fullNameVar = $("#fullName").val();
                if (fullNameVar === "") {
                    alert(" Name is Required Value");
                    $("#fullName").focus();
                    return "";
                }
                var classVar = $("#class").val();
                if (classVar === "") {
                    alert("Class is Required Value");
                    $("#class").focus();
                    return "";
                }

                var birthDateVar = $("#birthDate").val();
                if (birthDateVar === "") {
                    alert("Birth Date is Required Value");
                    $("#birthDate").focus();
                    return "";
                }

                var addVar = $("#add").val();
                if (addVar === "") {
                    alert("Address is Required Value");
                    $("#add").focus();
                    return "";
                }

                var enrolDateVar = $("#enrolDate").val();
                if (enrolDateVar === "") {
                    alert("Enrollment Date is Required Value");
                    $("#enrolDate").focus();
                    return "";
                }
                var jsonStrObj = {
                    rollno: rollnpVar,
                    fullName: fullNameVar,
                    class: classVar,
                    birthDate: birthDateVar,
                    add: addVar,
                    enrolDate: enrolDateVar
                };
                return JSON.stringify(jsonStrObj);
            }
            
            function saveRecNo2LS(jsonObj){
                var lvData=JSON.parse(jsonObj.data);
                localStorage.setItem("recno", lvData.rec_no);
            }
            
            function fillData(jsonObj)
            {
                saveRecNo2LS(jsonObj);
                var record=JSON.parse(jsonObj.data).record;
                $("#rollno").val(record.rollno);
                $("#fullName").val(record.fullName);
                $("#class").val(record.class);
                $("#birthDate").val(record.birthDate);
                $("#add").val(record.add);
                $("#enrolDate").val(record.enrolDate);
            }
            
            function getRollNoasJsonObj()
            {
                var rollno=$("#rollno").val();
                var jsonStr={
                    id: rollno
                };
                return JSON.stringify(jsonStr);
            }
            
            function getStudent()
            {
                 var rollNoJsonObj=getRollNoasJsonObj();
                 var getRequest=createGet_By_Request(connToken, stuDBName, stuRelationName, rollNoJsonObj );
                 jQuery.ajaxSetup({async: false});
                var resultObj = executeCommandAtGivenBaseUrl(getRequest,jpdbBaseURL, jpdbIRL);
             
                jQuery.ajaxSetup({async: true});
                if(resultObj.status===400){
                
                $("#save").prop("disabled", false);
                $("#reset").prop("disabled", false);
                $("#fullName").focus();
                
                } else if(resultObj.status===200){
                    
                 $("#rollno").prop("disabled",true);
                fillData(resultObj);
                $("#change").prop("disabled", false);
                $("#reset").prop("disabled", false);
                $("#rollno").focus();
                }
                 
            }
            function resetForm() {
                $("#rollno").val("");
                $("#fullName").val("");
                $("#class").val("");
                $("#birthDate").val("");
                $("#add").val("");
                $("#enrolDate").val("");
                
                $("#rollno").prop("disabled", false);
                $("#save").prop("disabled", true);
                $("#change").prop("disabled", true);
                $("#reset").prop("disabled", true);
                $("#rollno").focus();
            }

             
            function saveStudent()
            {
                var jsonStr = validateAndGetFormData();
                if (jsonStr === "") {
                    return;
                }
                var putReqStr = createPUTRequest(connToken, jsonStrObj, stuDBName, stuRelationName);
                jQuery.ajaxSetup({async: false});
                var resultObj = executeCommandAtGivenBaseUrl(putReqStr,jpdbBaseURL,jpdbIML);
                alert(JSON.stringify(resultObj));
                jQuery.ajaxSetup({async: true});
                resetForm();
                $("#rollno").focus();
            }
            
            function changeData()
            {
                $("#change").prop("disabled", true);
                jsonChg=validateAndGetFormData();
                var updateRequest=createUPDATERRecordRequest(connToken, jsonChg, stuDBName, stuRelationName, localStorage.getItem("recno"));
                jQuery.ajaxSetup({async: false});
                var resultObj = executeCommandAtGivenBaseUrl(updateRequest, jpdbBaseURL, jpdbIML);
               
                jQuery.ajaxSetup({async: true});
                console.log(resultObj);
                resetForm();
                $("#rollno").focus();
            }
            
        </script>
    </body>
</html>
