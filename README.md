prontera,150,170,5	script	Spec	833,{
function checkItem;			// check if player have all item required
function colorItemrequired;	// color the text. Red : not enough item, green otherwise
function deleteItem;		// delete all items required
function displayItemneed;	// display all items need at start
function getItemReward;		// give the items reward
function weightreq;			// check if your current weight is highter than weight high novice


	.@eac = eaclass();
	if ( num_rebirth == .reset_max ) {
		mes "You can only rebirth x"+ .reset_max +".";
		emotion e_gasp;
		close;
	}
	else if( NextJobExp || NextBaseExp || !( .@eac&EAJL_2 ) || !Upper ) {
		mes "You must be rebirth max level/max job level.";
		close;
	}

	mes "Items need :";
	displayItemneed();
	next;

	switch( select( "^777777~ Rebirth", "~ Informations", "~ Good bye^000000" ) ) {
		case 1:
			weightreq();
			checkItem();
			deleteItem();
			break;
		case 2:
			mes "You can only rebirth ^ff0000x"+ .reset_max +"^000000. You already rebirth ^ff0000x"+ num_rebirth +"^000000.";
			close;
		case 3:
			mes "Bye.";
			close;
	}
	num_rebirth += 1;
	jobchange Job_Novice;
	resetlvl 3;
	announce "[ Rebirth system ] : "+ strcharinfo(0) +" rebirth for the "+ num_rebirth +" time !", 0;
	close;



function checkItem {
	for ( ; .@i < .size_item; .@i += 2 )
		if ( countitem( .item_req[.@i] ) < .item_req[ .@i+1 ] + num_rebirth ) {
			mes "You don't have enought "+ getitemname( .item_req[.@i] ) +". ^ff0000["+ countitem( .item_req[.@i] ) +"/"+ ( .item_req[ .@i+1 ] + num_rebirth ) +"]^000000";
			close;
		}
	return;
}

function colorItemrequired {
	if ( countitem( .item_req[ getarg(0) ] ) < .item_req[ getarg(0)+1 ] + num_rebirth ) return "^ff0000";
	return "^00ff00";
}

function deleteItem {
	for ( ; .@i < .size_item; .@i += 2 )
		delitem .item_req[.@i], ( .item_req[ .@i+1 ] + num_rebirth );
	if ( num_rebirth >= .change_reward )
		delitem .add_item_req[0], ( .add_item_req[1] + num_rebirth - .change_reward );
	return;
}

function displayItemneed {
	for ( ; .@i < .size_item; .@i += 2 )
		mes colorItemrequired( .@i ) +" - x"+ ( .item_req[ .@i+1 ] + num_rebirth ) +" "+ getitemname( .item_req[.@i] );
	if ( num_rebirth >= .change_reward ) {
		if ( .add_item_req[1] + num_rebirth - .change_reward > countitem( .add_item_req[0] ) )
			.@color$ = "^ff0000";
		else
			.@color$ = "^00ff00";
		mes .@color$ +"- x"+ ( .add_item_req[1] + ( num_rebirth - .change_reward ) ) +" "+ getitemname( .add_item_req[0] );
	}
	return;
}

function getItemReward {
	.@rand = rand(.totalchance);
	.@r = 0;
	while ( ( .@rand -= .itemchance[.@r] ) >= 0 ) .@r++;
	getitem .reward[.@r], .@ramt;
	return;
}

function weightreq {
	if ( Weight > 20000 ) {
		mes "You have too much items on you. Your weight will be too high after rebirth.";
		close;
	}
	return;
}

OnInit:
	.reset_max = 50;		// how much reset max or set to value you need


// item required <item ID>, <number>
// Set the item reequired for rebirth to your need
	setarray .item_req, 501, 5,
						502, 2,
						503, 3;
	.size_item = getarraysize( .item_req );

// rewards <item ID>, <number>
	.@ramt = 1;
	setarray .reward, 501,502,503,504,505,506,507,508,509;
	setarray .rewardchance, 90,80,70,60,50,40,30,20,10;
	.@r = 0;
	while ( .reward[.@r] ) {
		.totalchance += .rewardchance[.@r];
		.@r++;
	}
	end;
}
