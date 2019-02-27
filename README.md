# DropDownbutton-using-streams-in-flutter
unable to load one drop down button upon selection of other dropdownbutton list item using streams in flutter 
problem in loading yardcodes... need help: I am using below code to display branchcode in one dropdown and upon selecting the branchcode. I must load yardcode in the other dropdown.. using API calls..(fetchBranchs(), fecthYards(ID)...
kindly help me.. As I do not understand where to invoke the event to load yardcode after selecting Branch code???

class LoginScreenHome extends State<LoginScreen>
{
List<Branch> _branches = [];
int selectedBranchID = 0;

List<Yard> _yards = [];
Yard selectedYard;

Future<List<Branch>> fetchBranchs() async {
final response = await http
    .get('http://dmsapi.logiconglobal.com/api/master/branch/branchlist/sct');
if (response.statusCode == 200) {
List branchs = json.decode(response.body);
_branches = branchs.map((branch) => new Branch.fromJson(branch)).toList();
return _branches;
} else
throw Exception('Failed to load Branch');
}

  Future<List<Yard>> fetchYards(branchID) async {
    var response = await http.get('http://dmsapi.logiconglobal.com/api/master/yard/branchyardlist/$branchID'); // + selectedBranch.branchID.toString() );
    if (response.statusCode == 200)
    { 
      List yards = json.decode(response.body);
      _yards = yards.map((yard) => new Yard.fromJson(yard)).toList();

      return _yards;
    } else
      throw Exception('Failed to load Yard');      
  }
   
  Widget build(context)
  {
    var bloc= Provider.of(context);
    var sizeHW = MediaQuery.of(context).size;
  
    TxtCss txtCss = new TxtCss();

    return new Stack(
      children: <Widget>[
        Row(
           mainAxisAlignment: MainAxisAlignment.start,
           crossAxisAlignment: CrossAxisAlignment.start,
           children:<Widget> [
              SizedBox(height:25.00),
              Row(children:<Widget>[Expanded(child: branchCodeList(bloc,txtCss))]),                               
              SizedBox(height:20.00),
              Row(children:<Widget>[Expanded(child: yardCodeList(bloc,txtCss))]),                                  
              ])
    ]);
  }

  Widget branchCodeList (Bloc bloc, TxtCss txtSS)
  {
    return StreamBuilder(
      stream: bloc.branchCode,
      builder: (context, snapshot) 
        { 
          return FutureBuilder(
            future: fetchBranchs(),
            builder: (context, branchsData)
            {
              return branchsData.hasData ? 
                DropdownButton<String>(
                  isDense: true,
                  items: branchsData.data
                    .map<DropdownMenuItem<String>>(
                      (Branch dropDownStringitem){ 
                        return DropdownMenuItem<String>(
                          value: dropDownStringitem.branchID.toString(), 
                          child: Text(dropDownStringitem.branchCode, 
                                  style: txtSS.txtRoboStyle(20),),);
                      },
                ).toList(),
                  
                onChanged: bloc.changeBranch, 
                value: snapshot.data,
                style: txtSS.txtRoboStyle(20.00),
                hint: Text('Branch Code '),
                elevation: 8,
              )
            :
              CircularProgressIndicator();
            },
          );
        }
    );
  }


  Widget yardCodeList(Bloc bloc, TxtCss txtSS) 
  {
    return StreamBuilder(
      stream: bloc.yardCode,  
      builder: (context, snapshot) 
        {
          bloc.branchCode.listen((onData) => selectedBranchID = int.parse(onData));
          return FutureBuilder(
            future: fetchYards(selectedBranchID), //fetchYards(getBrCode(bloc))
            builder: (context, yardsData){
              if (yardsData.hasData)
              {
                return DropdownButton<String>(
                  items: yardsData.data.map<DropdownMenuItem<String>>(
                          (Yard dropDownStringitem) {
                            return DropdownMenuItem<String>(
                                  value: dropDownStringitem.yardCode,
                                  child: Text(dropDownStringitem.description,
                                          style: txtSS.txtRoboStyle(20),)
                                  );
                          },
                        ).toList(),
                
                  onChanged: bloc.changeYard, 
                  value: snapshot.data,
                  hint: Text('Yard Code '),
                  style: txtSS.txtRoboStyle(20),
                  elevation: 8,
                );
              }
              else
              {
                return CircularProgressIndicator();
              }
            }
          );
        }
      );
  }

  Widget submitButton(Bloc bloc, TxtCss txtSS)
  {
    return StreamBuilder(
      stream: bloc.submitValid,
      builder: (context, snapshot) 
      {
        return RaisedButton(
          child: Text('Sign In',style: txtSS.btnTxtRoboStyle(20),),
          color: Colors.indigo,
          disabledColor: Colors.red,
          textColor: Colors.white,

          //shape: ,         
          onPressed: !snapshot.hasData ? null : () 
                                { //bloc.submit();
                                  Navigator.push(context, new MaterialPageRoute(
                                  builder: (context) =>
                                    new InitScreen())
                                  );
                                },
        );
      },  
    );

  }

}
