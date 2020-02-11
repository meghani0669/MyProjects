import { LightningElement, api, wire, track } from 'lwc';
import { getPicklistValues } from 'lightning/uiObjectInfoApi';
import { updateRecord, getRecordUi, getRecord, getFieldValue } from 'lightning/uiRecordApi';
import { ShowToastEvent } from 'lightning/platformShowToastEvent';
import {

    getMasterRecordTypeId,
    getRecordTypeId
} from './utils';


export default class custompathcomponent extends LightningElement {

    @api picklistField;
    @api objectApiName;
    @api recordId;
    @track currentstep;

    error;
    @track record;
    @track objectInfo;
    @track value;
    @track options;
    @track fieldLabel;
    @track showcurrentstage = false;
    @track showcompletestage = true;
    @track selectedstage;
    size;

    apiFieldName;
    optionlist = [];
    options1;

    _recordTypeId;


    @wire(getRecordUi, {
        recordIds: '$recordId',
        layoutTypes: 'Full',
        modes: 'View'
    })
    wiredRecordUI({ error, data }) {
        if (error) {
            this.errorMsg = error.body.message;
        }

        if (data && data.records[this.recordId]) {
            // set the record
            this.record = data.records[this.recordId];

            // set the object info
            this.objectInfo = data.objectInfos[this.objectApiName];
            this.apiFieldName = this.objectApiName + '.' + this.picklistField;
            // set the current record type
            const rtId = getRecordTypeId(this.record);
            this._recordTypeId = rtId
                ? rtId
                : getMasterRecordTypeId(this.objectInfo);
            window.console.log("rtId" + rtId);
        }
        window.console.log("recordtypeId" + this._recordTypeId);
        window.console.log(this.apiFieldName);
    }

    @wire(getPicklistValues, { recordTypeId: '$_recordTypeId', fieldApiName: '$apiFieldName' })
    getPicklistValues({ error, data }) {
        if (data) {

            // Map picklist values
            data.values.map(plivalue => {
                this.optionlist.push(plivalue.value);
                return this.optionlist;
            });
            this.size = this.optionlist.length;
            if (this.optionlist.indexOf(this.currentstep) === (this.size - 1)) {
                this.showcompletestage = false;
            }

            this.options = data.values.map(plValue => {
                return {
                    label: plValue.label,
                    value: plValue.value
                };
            });


        } else if (error) {
            // Handle error
            window.console.log('==============Error  ' + error);
            window.console.log(error);
        }
    }


    @wire(getRecord, { recordId: '$recordId', fields: '$apiFieldName' })
    wireRec({ error, data }) {
        if (error) {

            this.error = error;
        } else if (data) {

            // this.name = data.fields.Name.value;
            this.currentstep = data.fields[this.picklistField].value;


            // this.currentstep = getFieldValue(this.wireRec.data, this.apiFieldName);
            // window.console.log(this.currentstep);
        }
    }

    handleclick(event) {

        const stepname = event.target.label;
        window.console.log(stepname);
        if (this.currentstep === stepname) {
            this.showcompletestage = true
            this.showcurrentstage = false;
        }
        else {
            this.showcompletestage = false;
            this.showcurrentstage = true;
            this.selectedstage = stepname;
        }

    }


    handlecurrentupdate(event) {

        window.console.log("this.selectedstage" + this.selectedstage);

        const fields = {};
        fields.Id = this.recordId;
        fields[this.picklistField] = this.selectedstage;

        const recordInput = { fields };
        updateRecord(recordInput)
            // eslint-disable-next-line no-unused-vars
            .then(() => {

                this.showcurrentstage = false;
                this.showcompletestage = true;
                this.dispatchEvent(
                    new ShowToastEvent({
                        title: 'Success',
                        message: 'Record Is Updated',
                        variant: 'success',
                    }),
                );
            })
            .catch(error => {
                this.dispatchEvent(
                    new ShowToastEvent({
                        title: 'Error on data save',
                        message: error.message.body,
                        variant: 'error',
                    }),
                );
            });

    }
    handlecomplete(event) {


        var lastindex = (this.optionlist.length) - 2;
        var currentindex = this.optionlist.indexOf(this.currentstep);
        window.console.log(currentindex);
        if (currentindex === lastindex) {
            this.showcompletestage = false;


        }

        var nextindex = this.optionlist.indexOf(this.currentstep) + 1;

        this.nextStage = this.optionlist[nextindex];
        window.console.log(this.nextStage);
        const fields = {};
        fields.Id = this.recordId;
        fields[this.picklistField] = this.nextStage;

        const recordInput = { fields };
        updateRecord(recordInput)
            // eslint-disable-next-line no-unused-vars
            .then(() => {
                this.dispatchEvent(
                    new ShowToastEvent({
                        title: 'Success',
                        message: 'Record Is Updated',
                        variant: 'success',
                    }),
                );
            })
            .catch(error => {
                this.dispatchEvent(
                    new ShowToastEvent({
                        title: 'Error on data save',
                        message: error.message,
                        variant: 'error',
                    }),
                );
            });





    }


}
