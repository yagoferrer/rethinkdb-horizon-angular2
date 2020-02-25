# RethinkDB + Horizon + Angular2
Guide how to use RethinkDB, Horizon and Angular 2

## Mac OSX
Install RethinkDB using [brew](http://brew.sh/)
```bash
brew update && brew install rethinkDB
```

## Using docker
```bash
docker run -d -P --name rethink1 rethinkdb
```

follow instructions to run rethinkDB as a service

Install horizon using Node.js
```bash
npm install -g horizon
```

Install angular-cli
```bash
npm install -g angular-cli
```

Create a new Angular 2 project
```
ng new app
```

Create a new horizon project
```bash
hz init app2
```
That will create the `app2/.hz` folder. Copy this folder into `app`
```
mv app2/.hz app/
```

Go to the app/ directory
```
cd app
```

Install `concurrently`
```
npm install concurrently --save-dev
```

Edit the package.json file start script
```
"start": "concurrently \"hz serve --dev\" \"ng server\" ",
```

Edit `src/index.html` and add this line to the `<head>` block
```
<script src="http://127.0.0.1:8181/horizon/horizon.js"></script>
```

To run Horizon and ng server do:
```
npm start
```

Create a new Angular service similar to this
```typescript
import { Injectable } from '@angular/core';

@Injectable()
export class HorizonService {

  public horizon: any;
  status: {} | Boolean = false;

  connect() {     
    this.horizon = Horizon({ host: '127.0.0.1:8181'});    
    return new Promise((resolve, reject)=> {
      this.horizon.onReady((status)=> {
        this.status = status;        
        resolve(status);                
      });      
      this.horizon.connect();            
    });        
  }    
}
```

You can use it in your components like this
```typescript
import { Component, OnInit } from '@angular/core';
import { HorizonService } from './horizon.service';

@Component({
  moduleId: module.id,
  selector: 'app',
  templateUrl: 'app.component.html',
  styleUrls: ['app.component.css'],
  providers: [HorizonService]
})

export class AppComponent implements OnInit {
  list = [];
  constructor(private horizonService: HorizonService) {}
  ngOnInit() {    
    this.horizonService.connect().then(()=> {      
      this.horizonService.horizon('my_table').watch().subscribe((result)=> {
        console.log('result', result);
        this.list = result; 
      });      
    });    
  }     
}
```




