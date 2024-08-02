*------------------------------------------------------------*
User:                u63387163
Date:                August 02, 2024
Time:                03:28:12
Site:                70094220
Platform:            Linux
Maintenance Release: 9.04.01M7P080620
EM Version:          15.2
* 
*------------------------------------------------------------*
* Training Log
Date:                August 02, 2024
Time:                03:27:51
*------------------------------------------------------------*
15242  proc freq data=EMWS1.Report_VariableSet noprint;
15243  table ROLE*LEVEL/out=WORK.ReportMETA;
15244  run;
15245  proc print data=WORK.ReportMETA label noobs;
15246  var ROLE LEVEL COUNT;
15247  label ROLE = "%sysfunc(sasmsg(sashelp.dmine, meta_role_vlabel, NOQUOTE))" LEVEL = "%sysfunc(sasmsg(sashelp.dmine, meta_level_vlabel, NOQUOTE))" COUNT = "%sysfunc(sasmsg(sashelp.dmine, rpt_count_vlabel, NOQUOTE))";
15248  title9 ' ';
15249  title10 "%sysfunc(sasmsg(sashelp.dmine, rpt_varSummary_title  , NOQUOTE))";
15250  run;
15251  title10;
15252  %let EMEXCEPTIONSTRING=;
PERFORMANCE  DETAILS
15613  *------------------------------------------------------------*;
15614  * Report: Generation of macros and macro variables;
15615  * To see the code generated, set the EM_DEBUG macro variable to SOURCE or _ALL_;
15616  *------------------------------------------------------------*;
 
15617  %let EMEXCEPTIONSTRING=;
15618  *------------------------------------------------------------*;
15619  * TRAIN: Report;
15620  *------------------------------------------------------------*;
15621  %let EM_ACTION = TRAIN;
15622  %let syscc = 0;
15623  %macro main;
15624
15625    filename temp catalog 'sashelp.emutil.reporter_macros.source';
15626    %include temp;
15627    filename temp;
15628
15629    %setProperties;
15630
15631    %if %upcase(&EM_ACTION) = CREATE %then %do;
15632      filename temp catalog 'sashelp.emutil.reporter_create.source';
15633      %include temp;
15634      filename temp;
15635
15636      %create;
15637    %end;
15638
15639     %else
15640     %if %upcase(&EM_ACTION) = TRAIN %then %do;
15641
15642         filename temp catalog 'sashelp.emutil.reporter_train.source';
15643         %include temp;
15644         filename temp;
15645         %train;
15646     %end;
15647
15648     %doendm:
15649  %mend main;
15650
15651  %main;
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMUTIL.REPORTER_MACROS.SOURCE.
15654 +%macro SetProperties;
15656 +     /* reporting options */
15657 +     %em_checkmacro(name=EM_PROPERTY_FORMAT,         value=PDF , global=Y);
15658 +     %em_checkmacro(name=EM_PROPERTY_NODES ,         value=PATH, global=Y);
15659 +     %em_checkmacro(name=EM_PROPERTY_STYLE ,         value=DEFAULT, global=Y);
15660 +     %em_checkmacro(name=EM_PROPERTY_SHOWALL,        value=N, global=Y);
15662 +     %em_checkmacro(name=EM_PROPERTY_SUMMARIZATION,  value=Y, global=Y);
15663 +     %em_checkmacro(name=EM_PROPERTY_VARRANKING,     value=Y, global=Y);
15664 +     %em_checkmacro(name=EM_PROPERTY_CLASSIFICATION, value=Y, global=Y);
15665 +     %em_checkmacro(name=EM_PROPERTY_CROSSTABS,      value=Y, global=Y);
15666 +     %em_checkmacro(name=EM_PROPERTY_LIFTCHART,      value=Y, global=Y);
15667 +     %em_checkmacro(name=EM_PROPERTY_FITSTAT,        value=Y, global=Y);
15668 +     %em_checkmacro(name=EM_PROPERTY_COMPAREMDL,     value=Y, global=Y);
15670 +     %em_checkmacro(name=EM_PROPERTY_BASICOUTPUT,    value=N, global=Y);
15672 +    %em_checkmacro(name=EM_PROPERTY_textfont,   value=Arial, global=Y);
15673 +    %em_checkmacro(name=EM_PROPERTY_headersize, value=8, global=Y);
15674 +    %em_checkmacro(name=EM_PROPERTY_textsize,   value=6, global=Y);
15675 +    %em_checkmacro(name=EM_PROPERTY_titlesize,  value=10, global=Y);
15677 +%mend SetProperties;
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMUTIL.REPORTER_TRAIN.SOURCE.
15678 +
15679 +%macro train();
15680 +
15681 +    filename temp catalog 'sashelp.emutil.reporter_trainmacros.source'; %include temp; filename temp;
15682 +    filename temp catalog 'sashelp.emrpm.em_modelprops.source';         %include temp; filename temp;
15683 +
15684 +    /* Initialize property macro variables */
15685 +    %SetProperties;
15686 +
15687 +    /* if pdf or rtf files exist, delete these prior to recreating report */
15688 +    %EM_GETNAME(key=REPORT, type=FILE, extension=pdf);
15689 +    filename pdffile "&EM_USER_REPORT";
15690 +    %if %sysfunc(fexist(pdffile)) %then %do;
15691 +        %let rc = %sysfunc(fdelete(pdffile));
15692 +    %end;
15693 +    filename pdffile;
15694 +
15695 +    %EM_GETNAME(key=REPORT, type=FILE, extension=rtf);
15696 +    filename rtffile "&EM_USER_REPORT";
15697 +    %if %sysfunc(fexist(rtffile)) %then %do;
15698 +        %let rc = %sysfunc(fdelete(rtffile));
15699 +    %end;
15700 +    filename rtffile;
15701 +
15702 +    /* create report */
15703 +
15704 +    %EM_REPORT_CREATE(
15705 +        path        = &EM_NODEDIR,
15706 +        lib         = &EM_LIB,
15707 +        startnodeid = &EM_NODEID,
15708 +        showall     = &EM_PROPERTY_SHOWALL,
15709 +        nodes       = &EM_PROPERTY_NODES,
15710 +        format      = &EM_PROPERTY_FORMAT,
15711 +        style       = &EM_PROPERTY_STYLE
15712 +    );
15713 +
15714 +    %if &EMEXCEPTIONSTRING ne %then %do; %goto doendm; %end;
15715 +
15716 +    %EM_REPORT_RESULTS(
15717 +        path        = &EM_NODEDIR,
15718 +        lib         = &EM_LIB,
15719 +        startnodeid = &EM_NODEID,
15720 +        showall     = &EM_PROPERTY_SHOWALL,
15721 +        nodes       = &EM_PROPERTY_NODES,
15722 +        format      = &EM_PROPERTY_FORMAT,
15723 +        style       = &EM_PROPERTY_STYLE
15724 +    );
15725 +
15726 +    %doendm:
15727 +
15728 +%mend train;
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMUTIL.REPORTER_TRAINMACROS.SOURCE.
15729 +/*---------------------------------------------------------*/
15730 + *  EM_REPORT_CREATE;
15731 + *   all work needed to start the documents;
15732 + *  - identify directory location;
15733 + *  - clear directory of em report files;
15734 + *  - initialize document options;
15735 + *  - setup ODS system;
15736 +/*---------------------------------------------------------*/
15737 +%macro EM_REPORT_CREATE(
15738 +    path=,             /*-- output location             ---*/
15739 +    lib=,              /*-- em workspace library        ---*/
15740 +    startnodeid=,      /*-- starting nodeid             ---*/
15741 +    showall=,          /*-- show all content Y or N     ---*/
15742 +    nodes=,            /*-- nodes to generate report on ---*/
15743 +    format=,           /*-- format for generated report ---*/
15744 +    style=             /*--- style for ODS statement    ---*/
15745 +) ;
15747 +    filename temp catalog 'sashelp.emutil.reporter_plotmacros.source';      %include temp; filename temp;
15748 +    filename temp catalog 'sashelp.emutil.reporter_printmacros.source';     %include temp; filename temp;
15749 +    filename temp catalog 'sashelp.emutil.reporter_rpmmacros.source';       %include temp; filename temp;
15750 +    filename temp catalog 'sashelp.emutil.em_gettrainpath.source' ;         %include temp; filename temp;
15751 +    filename temp catalog 'sashelp.emutil.reporter_tagset_template.source'; %include temp; filename temp;
15753 +    /*--- 1. set global parameters ---*/
15755 +    %GLOBAL EM_REPORT_PATH    ; %let EM_REPORT_PATH    = &PATH ;
15756 +    %GLOBAL EM_REPORT_SHOWALL ; %let EM_REPORT_SHOWALL = %upcase(&showall) ;
15757 +    %GLOBAL EM_REPORT_LIB     ; %let EM_REPORT_LIB     = &lib;
15758 +    %GLOBAL EM_REPORT_NODES   ; %let EM_REPORT_NODES   = &nodes;
15759 +    %GLOBAL EM_REPORT_FORMAT  ; %let EM_REPORT_FORMAT  = &format;
15760 +    %GLOBAL EM_REPORT_SYTLE   ; %let EM_REPORT_STYLE   = &style;
15762 +    /*--- 2. set global constants ---*/
15764 +    %GLOBAL EM_REPORT_GDEVICE;
15765 +    %GLOBAL EM_REPORT_PRINT_MAXVARS;
15766 +    %GLOBAL EM_REPORT_TEXT_FONT;
15767 +    %GLOBAL EM_REPORT_TEXT_SIZE;
15768 +    %GLOBAL EM_REPORT_TITLE_SIZE;
15769 +    %GLOBAL EM_REPORT_HEADER_SIZE;
15770 +    %GLOBAL EM_REPORT_NODE_FONT;
15771 +    %GLOBAL EM_REPORT_TITLE;
15772 +    %GLOBAL EM_REPORT_FOOTNOTE1;
15773 +    %GLOBAL EM_REPORT_FOOTNOTE2;
15775 +    %global EM_REPORT_SUPPRESS_DATE; %let EM_REPORT_SUPPRESS_DATE =;
15777 +    %global RPM_REPORT_MODELNODE RPM_REPORT_SCORENODE RPM_REPORT_MDLCOMP RPM_REPORT_VALIDSUMMARY;
15779 +    %if "&EM_REPORT_GDEVICE"       eq ""  %then %let EM_REPORT_GDEVICE = GIF;
15780 +    %if "&EM_REPORT_PRINT_MAXVARS" eq ""  %then %let EM_REPORT_PRINT_MAXVARS= 40;
15781 +    %if "&EM_REPORT_TEXT_FONT"     eq ""  %then %do;
15782 +        %let EM_REPORT_TEXT_FONT= %sysfunc(getlocale(FTITLE, &EM_PROPERTY_TEXTFONT));
15783 +        %let EM_REPORT_TEXT_FONT= %sysfunc(tranwrd(&EM_REPORT_TEXT_FONT,%str(<ttf>),%str()));
15784 +    %end;
15785 +    %if "&EM_REPORT_NODE_FONT"     eq ""  %then %do;
15786 +        %let EM_REPORT_NODE_FONT = %sysfunc(getlocale(FTITLE, SIMPLEX)) ;
15787 +        %let EM_REPORT_NODE_FONT = %sysfunc(tranwrd(&EM_REPORT_NODE_FONT,%str(<ttf>),%str()));
15788 +    %end;
15789 +    %if "&EM_REPORT_HEADER_SIZE"   eq ""  %then %let EM_REPORT_HEADER_SIZE = &EM_PROPERTY_HEADERSIZE;
15790 +    %if "&EM_REPORT_TEXT_SIZE"     eq ""  %then %let EM_REPORT_TEXT_SIZE   = &EM_PROPERTY_TEXTSIZE;
15791 +    %if "&EM_REPORT_TITLE_SIZE"    eq ""  %then %let EM_REPORT_TITLE_SIZE  = &EM_PROPERTY_TITLESIZE;
15793 +    /*--- 3. text for title statements ---*/
15795 +    %global EM_REPORT_PROJECT_TEXT ; %let EM_REPORT_PROJECT_TEXT= %superq(EM_PROJECTNAME);
15796 +    %global EM_REPORT_DIAGRAM_TEXT ; %let EM_REPORT_DIAGRAM_TEXT= %superq(EM_WSNAME);
15798 +    %EM_REPORT_NODE_TEXT(&startnodeid) ;
15800 +    /*--- 4. create output filename ---*/
15802 +    %EM_REGISTER(key=REPORT, type=FILE, extension=&EM_REPORT_FORMAT);
15804 +    /*--- 5. Allways get the path for showing the score code --*/
15806 +    %let FLOW= ;            /*--- ignore for now ---*/
15807 +    %let PUBLISH=;          /*--- ignore for now ---*/
15808 +    %let EM_LIB=&lib ;      /*--- used by the macro */
15810 +    %if &EM_REPORT_NODES ne SUMMARY %then %do;
15811 +        %EM_PATH(nodeid=&startnodeid, outds=emreportpath, nodes=&EM_REPORT_NODES);
15812 +    %end;
15813 +    %else %do;
15814 +        %EM_PATH(nodeid=&startnodeid, outds=emreportpath, nodes=PATH);
15815 +    %end;
15817 +    data emreportpath;
15818 +        set emreportpath;
15819 +        rename nodelabel=nodeid;
15820 +        nodeIndex = _n_;
15821 +    run;
15823 +    /*--- 6. create new tagset templates needed for tree, icicle and pie charts */
15825 +    *** create new style template to address fonts in ods text;
15827 +    ods path(prepend) work.templat(update);
15828 +    proc template ;
15829 +        define style styles.em_style / store=work.templat;
15830 +        parent= styles.&EM_REPORT_STYLE;
15832 +            style usertext from usertext /
15833 +                foreground=black font_size=&EM_REPORT_TITLE_SIZE pt font_face="&EM_REPORT_TEXT_FONT" font_weight=bold
15834 +            ;
15835 +            style systemtitle from systemtitle /
15836 +              color=colors("systitlefg") backgroundcolor=colors("systitlebg")
15837 +              fontsize=&EM_REPORT_TITLE_SIZE pt font_face="&EM_REPORT_TEXT_FONT" font_weight=bold
15838 +            ;
15839 +            style systemfooter from systemfooter /
15840 +              color=colors("systitlefg") backgroundcolor=colors("systitlebg")
15841 +              font_face="&EM_REPORT_TEXT_FONT"
15842 +            ;
15843 +            style GraphFonts from GraphFonts /
15844 +                'GraphTitleFont'    = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TITLE_SIZE pt)
15845 +                'GraphTitle1Font'   = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TITLE_SIZE pt)
15846 +                'GraphLabelFont'    = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_HEADER_SIZE pt)
15847 +                'GraphFootnoteFont' = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15848 +                'GraphValueFont'    = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15849 +                'GraphDataFont'     = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15850 +            ;
15851 +            style Fonts from Fonts/
15852 +                'TitleFont'           = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TITLE_SIZE pt)
15853 +                'TitleFont2'          = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TITLE_SIZE pt)
15854 +                'StrongFont'          = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15855 +                'EmphasisFont'        = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15856 +                'headingFont'         = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15857 +                'headingEmphasisFont' = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15858 +                'FixedEmphasisFont'   = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15859 +                'FixedStrongFont'     = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15860 +                'FixedHeadingFont'    = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15861 +                'FixedFont'           = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15862 +                'BatchFixedFont'      = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15863 +                'docFont'             = ("&EM_REPORT_TEXT_FONT", &EM_REPORT_TEXT_SIZE pt)
15864 +            ;
15865 +            style GraphTitleText from GraphTitleText / font_face="&EM_REPORT_TEXT_FONT";
15866 +            style data           from data           / font_size=&EM_REPORT_TEXT_SIZE pt;
15867 +            style header         from header         / font_size=&EM_REPORT_HEADER_SIZE pt;
15868 +            style rowheader      from header;
15869 +        end;
15870 +    run; quit;
15872 +    /*--- 7. start report / destination configurations ---*/
15874 +    *** options for page layout;
15875 +    options nodate nocenter number ls=97 ;
15877 +    *** get file name;
15878 +    %EM_GETNAME(key=REPORT, type=FILE, extension=&EM_REPORT_FORMAT);
15879 +    %let file = &EM_USER_REPORT;
15881 +    *** turn off LISTING and turn on the specified destination;
15882 +    ods listing close;
15883 +    ods graphics / height=4.5in;
15884 +    ods &EM_REPORT_FORMAT file="&file" style=styles.em_style
15885 +        %if &EM_REPORT_FORMAT eq PDF %then %do; startpage=NO uniform NOTOC %end;
15886 +        %if &EM_REPORT_FORMAT eq RTF %then %do; startpage=NEVER BODYTITLE  %end;
15887 +    ;
15889 +    *** titles and footnotes;
15890 +    ods &EM_REPORT_FORMAT text=" ";
15891 +    %if "&EM_REPORT_TITLE" ne "" %then %do;
15892 +        ods &EM_REPORT_FORMAT text="&EM_REPORT_TITLE";
15893 +    %end;
15894 +    %else %do;
15895 +        ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))";
15896 +    %end;
15898 +    %if "&EM_REPORT_FOOTNOTE1" ne "" %then %do; footnote1 "&EM_REPORT_FOOTNOTE1"; %end;
15899 +    %if "&EM_REPORT_FOOTNOTE2" ne "" %then %do; footnote2 "&EM_REPORT_FOOTNOTE2"; %end;
15901 +    /*--- 8. verify that valid flow exists for SUMMARY (RPM) report ---*/
15902 +    /*--- if not, SUMMARY style report can not be generated and PATH report will be done instead ---*/
15904 +    %EM_REPORT_validSummaryPath(startNodeID = &startNodeID);
15906 +    %if ((&EM_REPORT_NODES eq SUMMARY ) AND(&RPM_REPORT_VALIDSUMMARY eq 1))%then %do;
15907 +        %local hprpm_hpassess;
15908 +        %let   hprpm_hpassess = &em_lib..&RPM_REPORT_MODELNODE._hpassess;
15909 +        %if %sysfunc(exist(&hprpm_hpassess)) %then %do;
15910 +            filename temp catalog 'sashelp.hpdm.hpdm_scorecard.source'; %include temp; filename temp;
15911 +            %EM_REPORT_SUMMARY(runHP=1);
15912 +        %end;
15913 +        %else %do;
15914 +            %EM_REPORT_SUMMARY(runHP=0);
15915 +        %end;
15916 +        %goto create_endline;
15917 +    %end;
15918 +    %else %do;
15919 +        %if &EM_REPORT_NODES eq SUMMARY %then %let EM_REPORT_NODES= PATH;
15920 +    %end;
15922 +    /*--- 9. output report information (header of the report)---*/
15924 +    ods proclabel "%sysfunc(sasmsg(sashelp.dmine, rpt_reportinfo_title, NOQUOTE))";
15925 +    data _null_;
15926 +        call symput('report_date', %nrbquote(strip(put(date(),     NLDATE.))));
15927 +        call symput('report_time', %nrbquote(strip(put(datetime(), NLTIME.))));
15928 +    run;
15930 +    data _null_;
15931 +        file print ;
15932 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_user_note, NOQUOTE, &sysuserid))" ;
15933 +        %if ((&EM_REPORT_SUPPRESS_DATE eq ) OR (%index(&EM_REPORT_SUPPRESS_DATE, Y) < 1)) %then %do;
15934 +            put "%sysfunc(sasmsg(sashelp.dmine, rpt_date_note, NOQUOTE, %nrbquote(&report_time), &report_date))" ;
15935 +        %end;
15936 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_project_note, NOQUOTE, &EM_REPORT_PROJECT_TEXT)) " ;
15937 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_diagram_note, NOQUOTE, %nrstr(&EM_REPORT_DIAGRAM_TEXT)))" / ;
15938 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_startnode_note, NOQUOTE, &startnodeid))";
15939 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_nodelabel_note, NOQUOTE, %nrbquote(&em_report_node_text)))" ;
15940 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_nodes_note, NOQUOTE, &EM_REPORT_NODES))" ;
15941 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_showall_note, NOQUOTE, &EM_REPORT_showall))" /;
15942 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_format_note, NOQUOTE, &EM_REPORT_FORMAT))" ;
15943 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_style_note, NOQUOTE, &EM_REPORT_STYLE))" /;
15944 +    run ;
15946 +    /*--- 10. generate the workspace plot ---*/
15948 +    %em_report_workspace_xml;
15949 +    %em_getname(key=PFD, type=FILE, extension=XML);
15950 +    %em_report_makeds(&EM_USER_PFD, xmldata);
15951 +    %em_report_workspace_render;
15953 +    /*--- 11. identify the nodes to report                                  ---*/
15954 +    /*--- merged and modified from three previous macros:                   ---*/
15955 +    /*--- EM_REPORT_PATH, EM_REPORT_PREDECESORS EM_REPORT_PREDECESORS_COUNT ---*/
15957 +    %global EM_REPORT_NODENUM EM_REPORT_NODELIST;
15958 +    %let EM_REPORT_NODENUM = 0;
15960 +    %if &EM_REPORT_NODES eq PATH or &EM_REPORT_NODES eq ALL
15961 +    %then %do;
15962 +        %if %sysfunc(exist(emreportpath)) %then %do;
15963 +            proc sql noprint;
15964 +                select count(*) into :EM_REPORT_NODENUM
15965 +                    from emreportpath where nodeID ne " ";
15966 +                %if &EM_REPORT_NODENUM gt 0 %then %do;
15967 +                    select strip(nodeID) into :EM_REPORT_NODELIST separated by ' '
15968 +                        from emreportpath where nodeID ne " " order by nodeIndex;
15969 +                %end;
15970 +            quit;
15971 +        %end;
15972 +    %end;
15973 +    %else %if &EM_REPORT_NODES eq PREDECESSOR
15974 +    %then %do ;
15975 +        proc sql noprint;
15976 +            select count(*) into :EM_REPORT_NODENUM
15977 +                from &EM_REPORT_LIB..em_dgraph where upcase(strip(to)) eq upcase(strip("&startnodeid"));
15978 +            %if &EM_REPORT_NODENUM gt 0 %then %do;
15979 +                select strip(from) into :EM_REPORT_NODELIST separated by ' '
15980 +                    from &EM_REPORT_LIB..em_dgraph where upcase(strip(to)) eq upcase(strip("&startnodeid"));
15981 +            %end;
15982 +        quit;
15983 +    %end;
15984 +    %else %if &EM_REPORT_NODES eq 0 %then %do;
15985 +        %let EM_REPORT_NODENUM = 1;
15986 +        %let EM_REPORT_NODELIST = &startnodeid;
15987 +    %end;
15989 +    %if &EM_REPORT_NODENUM gt 0 %then %do;
15990 +        %do n = 1 %to &EM_REPORT_NODENUM;
15991 +            %EM_REPORT_NODE(nodeid=%scan(&EM_REPORT_NODELIST,&n,' '));
15992 +        %end;
15993 +    %end;
15995 +    /*--- 12. close report and ODS destinations---*/
15997 +    %create_endline:;
15999 +    data _NULL_ ;
16000 +        file print;
16001 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_endreport_note, NOQUOTE))" ;
16002 +    run;
16004 +    title1; title2; title3 ;
16005 +    %if "&EM_REPORT_FOOTNOTE1" ne "" %then %do; footnote1; %end;
16006 +    %if "&EM_REPORT_FOOTNOTE2" ne "" %then %do; footnote2; %end;
16007 +    ods graphics / reset=height;
16008 +    ods listing;
16009 +    ods &EM_REPORT_FORMAT close;
16011 +%mend EM_REPORT_CREATE ;
16013 +/*---------------------------------------------------------*/
16014 + * EM_REPORT_NODE ;
16015 + *     create a report for a node on an EM diagram ;
16016 +/*---------------------------------------------------------*/
16017 +%macro EM_REPORT_NODE(nodeid=) ;
16019 +    %if not %sysfunc(exist(&em_report_lib..&nodeid._emreport))
16020 +    %then %do ;
16021 +        %put NOTE: Node report data set (&em_report_lib..&nodeid._emreport) is not defined.;
16022 +        %goto node_skip;
16023 +    %end ;
16025 +    /*--- 1. set global parameters ---*/
16027 +    %global EM_REPORT_NODEID EM_REPORT_KEY_WHERECLAUSE
16028 +            EM_REPORT_TTL1 EM_REPORT_TTL2 EM_REPORT_TTL3
16029 +    ;
16030 +    %let EM_REPORT_NODEID= &nodeid ;
16032 +    /*--- 2. preparations: Node Text; Path; Notes; Num of Prev. Nodes ---*/
16034 +    /* 2.1 get node text;*/
16035 +    %EM_REPORT_NODE_TEXT(&nodeid) ;
16037 +    /* 2.2 get the em path to this node;*/
16038 +    %EM_gettrainpath(nodeid=&nodeid,outpath=_tmp_emrnp);
16040 +    %if %sysfunc(exist(_tmp_emrnp))
16041 +    %then %do;
16042 +        data _null_;
16043 +            set _tmp_emrnp end=eof ;
16044 +            length path $1000 ;
16045 +            retain path ' ';
16046 +            if _n_ eq 1
16047 +                then path = strip(nodelabel) ;
16048 +                else path = strip(path) !! " => " !! strip(nodelabel);
16049 +            if eof then call symput('print_path', strip(path));
16050 +        run;
16051 +    %end ;
16052 +    %else %do;
16053 +        %let print_path= Unknown;
16054 +    %end ;
16056 +    /* 2.3 retrieve location of notes file;*/
16057 +    %let temp = %nrbquote(%sysfunc(pathname(&EM_REPORT_LIB)));
16058 +    %let path = %nrbquote(%sysfunc(tranwrd(&temp, %str(%"),"")));
16059 +    %let file = EMNOTES.txt;
16060 +    %let notesfile = %nrbquote(&path&em_dsep&nodeid&em_dsep&file);
16062 +    /* 2.4 predecessor node counts */
16063 +    proc sql noprint;
16064 +        select count(*) into :EM_REPORT_PREDECESSORS_COUNT
16065 +            from &EM_REPORT_LIB..EM_DGRAPH where upcase(strip(to)) eq upcase(strip("&nodeid"));
16066 +    quit;
16068 +    %if ((&EM_REPORT_PREDECESSORS_COUNT eq 0) or (&EM_REPORT_NODES eq 0) or (&EM_REPORT_NODES eq PREDECESSOR))
16069 +        %then %let varlist=1;
16070 +        %else %let varlist=0;
16072 +    %if %substr(%upcase(%sysfunc(strip(&nodeid))), 1, 7) eq MDLCOMP
16073 +        %then %let varlist = 0;
16075 +    /*--- 3. beginning section of a node report ---*/
16077 +    /* 3.1 force page break at the beginning of each node */
16079 +    /* sample output of a beginning section
16080 +        -----------------------------------
16081 +        -  SAS Enterprise Miner Report    -
16082 +        -                                 -
16083 +        -  Node=Regression                -
16084 +        -  Summary                        -
16085 +        -                                 -
16086 +        -  Node id = Reg                  -
16087 +        -  Node label = Regression        -
16088 +        -  Meta Path = Ids => Part => Reg -
16089 +        -  Notes =                        -
16090 +        -----------------------------------*/
16092 +    ods &EM_REPORT_FORMAT startpage=now;
16094 +    ods &EM_REPORT_FORMAT text=" ";
16095 +    %if "&EM_REPORT_TITLE" ne ""
16096 +        %then %do; ods &EM_REPORT_FORMAT text= "&EM_REPORT_TITLE"; %end;
16097 +        %else %do; ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))"; %end;
16099 +    ods &EM_REPORT_FORMAT text=" ";
16100 +    ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text)))";
16101 +    ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_summary_title, NOQUOTE))";
16103 +    data _null_ ;
16104 +        file print;
16105 +        put " ";
16106 +        %let note = %sysfunc(sasmsg(sashelp.dmine, rpt_rptnodeid_vlabel,   NOQUOTE, &nodeid));                         put "&note";
16107 +        %let note = %sysfunc(sasmsg(sashelp.dmine, rpt_rptnodelbl_vlabel,  NOQUOTE, %nrbquote(&em_report_node_text))); put "&note";
16108 +        %let note = %sysfunc(sasmsg(sashelp.dmine, rpt_rptmetapath_vlabel, NOQUOTE, &print_path));                     put "&note";
16109 +        %let note = %sysfunc(sasmsg(sashelp.dmine, rpt_rptnotes_vlabel,    NOQUOTE));                                  put "&note";
16110 +        put " ";
16111 +    run;
16113 +    %if "&notesfile" ne "" %then %do;
16114 +        filename x "&notesfile" encoding='utf-8' NOBOM;
16115 +        data _null_;
16116 +            length string $1000;
16117 +            file PRINT;
16118 +            fid = fopen('x');
16119 +            do while (^fread(fid));
16120 +                rc= fget(fid, string, frlen(fid));
16121 +                string = tranwrd(string, "'", "''");
16122 +                if string eq '' then string = ' ';
16123 +                put string;
16124 +            end;
16125 +            fid = fclose(fid);
16126 +            put " ";
16127 +        run;
16128 +        filename x;
16129 +    %end;
16131 +    /*--- 4. property table of a node report ---*/
16132 +    %EM_REPORT_PROPERTIES(catalog=&em_report_lib..&nodeid, dsname=properties);
16134 +    /*--- 5. datasource table of a node report ---*/
16135 +    %if &varlist eq 1 and %sysfunc(exist(&EM_REPORT_LIB..&EM_REPORT_NODEID._EMTMETADEFAULT)) %then %do;
16136 +        %EM_REPORT_DATASOURCE;
16137 +    %end;
16139 +    /*--- 6. varlist table of a node report ---*/
16140 +    %EM_REPORT_VARS(varlist=&varlist) ;
16142 +    /* --- 7. the itemized output of EMREPORT TLFs ---*/
16144 +    /* 7.1 get number of items */
16145 +    proc sql noprint;
16146 +        select max(ID) into: numitems from &em_report_lib..&nodeid._emreport;
16147 +    quit;
16149 +    /* 7.2 define and initialize global KEYs */
16150 +    proc sql noprint;
16151 +        select count(distinct key) into :allnumkeys
16152 +            from &em_report_lib..&nodeid._emreport;
16153 +        select distinct key into :allnamekey1-:allnamekey%left(&allnumkeys)
16154 +            from &em_report_lib..&nodeid._emreport;
16155 +    quit;
16157 +    %do i = 1 %to &allnumkeys;
16158 +        %global EM_REPORT_KEY_&&&allnamekey&i;
16159 +        %let EM_REPORT_KEY_&&&allnamekey&i = ;
16160 +    %end;
16161 +    %global EM_REPORT_KEY_YREF EM_REPORT_KEY_XREF EM_REPORT_KEY_Y EM_REPORT_KEY_Y2 EM_REPORT_KEY_X
16162 +            EM_REPORT_KEY_LATTICEY EM_REPORT_KEY_LATTICEX EM_REPORT_KEY_WHERE EM_REPORT_KEY_SUBGROUP
16163 +            EM_REPORT_KEY_FREQUENCY EM_REPORT_KEY_GROUP EM_REPORT_KEY_Y2REF EM_REPORT_KEY_LATTICETYPE
16164 +            EM_REPORT_KEY_ORIENTATION
16165 +    ;
16166 +    %let EM_REPORT_KEY_AUTODISPLAY = N;
16167 +    %let EM_REPORT_KEY_XREF        = ;
16168 +    %let EM_REPORT_KEY_YREF        = ;
16169 +    %let EM_REPORT_KEY_Y2REF       = ;
16170 +    %let EM_REPORT_KEY_X           = ;
16171 +    %let EM_REPORT_KEY_Y           = ;
16172 +    %let EM_REPORT_KEY_Y2          = ;
16173 +    %let EM_REPORT_KEY_LATTICEX    = ;
16174 +    %let EM_REPORT_KEY_LATTICEY    = ;
16175 +    %let EM_REPORT_KEY_WHERE       = ;
16176 +    %let EM_REPORT_KEY_SUBGROUP    = ;
16177 +    %let EM_REPORT_KEY_FREQUENCY   = ;
16178 +    %let EM_REPORT_KEY_GROUP       = ;
16179 +    %let EM_REPORT_KEY_LATTICETYPE = ;
16180 +    %let EM_REPORT_KEY_ORIENTATION = ;
16182 +    /* 7.3 loop over items in the EM_REPORT dataset */
16184 +    %do index = 1 %to &numitems ;
16186 +        /* 7.3.1 initialize all key macro variables for each item */
16188 +        %do i = 1 %to &allnumkeys;
16189 +            %let EM_REPORT_KEY_&&&allnamekey&i = ;
16190 +        %end;
16191 +        %let EM_REPORT_KEY_AUTODISPLAY = N;
16192 +        %let EM_REPORT_KEY_WHERE       = ;
16193 +        %let EM_REPORT_KEY_WHERECLAUSE = ;
16194 +        %let EM_REPORT_TTL1            = ;
16195 +        %let EM_REPORT_TTL2            = ;
16196 +        %let EM_REPORT_TTL3            = ;
16197 +        %let EM_REPORT_KEY_XREF        = ;
16198 +        %let EM_REPORT_KEY_YREF        = ;
16199 +        %let EM_REPORT_KEY_Y2REF       = ;
16200 +        %let EM_REPORT_KEY_X           = ;
16201 +        %let EM_REPORT_KEY_Y           = ;
16202 +        %let EM_REPORT_KEY_Y2          = ;
16203 +        %let EM_REPORT_KEY_LATTICEX    = ;
16204 +        %let EM_REPORT_KEY_LATTICEY    = ;
16205 +        %let EM_REPORT_KEY_SUBGROUP    = ;
16206 +        %let EM_REPORT_KEY_FREQUENCY   = ;
16207 +        %let EM_REPORT_KEY_GROUP       = ;
16208 +        %let EM_REPORT_KEY_LATTICETYPE = ;
16209 +        %let EM_REPORT_KEY_ORIENTATION = ;
16211 +        %let item_skip_code = 1;
16213 +        data _null_;
16214 +            set &em_report_lib..&nodeid._emreport(where=(id eq &index));
16215 +            call symput("EM_REPORT_KEY_"!!strip(upcase(key)), strip(value));
16216 +        run;
16218 +        %if "&EM_REPORT_KEY_VIEWTYPE" eq "MULTIPLESCATTER"
16219 +            %then %let EM_REPORT_KEY_VIEWTYPE = SCATTER;
16221 +        /* special treatment for EM_REPORT_KEY_Y in overlaid line plot */
16222 +        %if "&EM_REPORT_KEY_VIEWTYPE" eq "PLOT" or "&EM_REPORT_KEY_VIEWTYPE" eq "SCATTER"
16223 +            or "&EM_REPORT_KEY_VIEWTYPE" eq "LINEPLOT"
16224 +            or "&EM_REPORT_KEY_LATTICETYPE" eq "LINEPLOT" or "&EM_REPORT_KEY_LATTICETYPE" eq "PLOT"
16225 +        %then %do;
16226 +            proc sql noprint;
16227 +                select distinct value into :EM_REPORT_KEY_Y separated by ' '
16228 +                    from &em_report_lib..&nodeid._emreport(where=(id eq &index))
16229 +                    where upcase(key) eq "Y";
16230 +            quit;
16231 +        %end;
16233 +        %put ==&INDEX : Viewtype = &EM_REPORT_KEY_VIEWTYPE &EM_REPORT_KEY_DESCRIPTION ;
16235 +        /* 7.3.2 set EM_REPORT_KEY_WHERE as EM_REPORT_KEY_WHERECLAUSE */
16237 +        %if "&EM_REPORT_KEY_WHERECLAUSE" ne "" and "&EM_REPORT_KEY_WHERE" eq ""
16238 +        %then %do;
16239 +            %let EM_REPORT_KEY_WHERE = %str(&EM_REPORT_KEY_WHERECLAUSE);
16240 +        %end;
16241 +        %else %if "&EM_REPORT_KEY_WHERECLAUSE" eq "" and "&EM_REPORT_KEY_WHERE" ne ""
16242 +        %then %do;
16243 +            %let EM_REPORT_KEY_WHERE = %str(&EM_REPORT_KEY_WHERE);
16244 +        %end;
16245 +        %else %do;
16246 +            %let EM_REPORT_KEY_WHERE = ;
16247 +        %end;
16249 +        %if "&EM_REPORT_KEY_WHERE" ne "" %then %do;
16250 +            %let EM_REPORT_KEY_WHERECLAUSE = %str((where=(&EM_REPORT_KEY_WHERE)));
16251 +        %end;
16253 +        /* 7.3.3 skip this TLF item if the source data set does not exist */
16254 +        %if not %sysfunc(exist(&EM_REPORT_LIB..&EM_REPORT_KEY_FILE)) %then %do;
16255 +            %let item_skip_code = 0;
16256 +        %end;
16258 +        /* 7.3.4 skip the TLF item as documented in the previous version of the macro */
16259 +        /*       or just print the TLF item as documented, or use special macros      */
16261 +        data _null_;
16262 +            format type $100. desc $200.;
16263 +            type = strip(upcase(symget('EM_REPORT_KEY_VIEWTYPE')));
16264 +            desc = strip(upcase(symget('EM_REPORT_KEY_DESCRIPTION')));
16266 +            if  (type in ('SCATTER')   and desc in ('STATPLOT' 'RULEPATHITEM' 'RULEPATHSTAT' 'CONFPLOT' 'INPUTMEAN'))
16267 +             or (type in ('PLOT')      and desc in ('SEASON' 'TREND' 'CORRSTAT' 'DECOMP'))
16268 +             or (type in ('HISTOGRAM') and desc in ('WEIGHT' 'MAP' 'ESTIMATEGRID' 'CROSSCCFMAP' 'CROSSCCFNORMMAP' 'CROSSCCOVMAP'))
16269 +             or (type in ('BAR')       and desc in ('VIP' 'ABSESTIMATE' 'ABSSTDESTIMATE' 'PRINCOEFFPLOT'))
16270 +             or (type in ('LATTICE')   and desc in ('ESTIMATEGRID' 'ASSESSRANKGRID' 'CORRPLOT'))
16271 +                then call symput('item_skip_code','2');
16273 +            /* ASSESSRANKGRID : content handled in %EM_REPORT_PLOT - from old macro comments*/
16274 +            /* CORRPLOT: empty macro report_corrplot in old macro */
16276 +            if  (type in ('HISTOGRAM') and desc in ('CLUSCORR' 'VARCORR'))
16277 +             or (type in ('BAR')       and desc in ('ESTIMATE' 'CORRSTRUCT' 'FITSTATPLOT'))
16278 +             or (type in ('LATTICE')   and desc in ('CHIPLOT' 'WORTH'))
16279 +                then call symput('item_skip_code','3');
16281 +            if (type in ('PLOT')    and desc in ('ASSESSRANKOVERLAY'))
16282 +                then call symput('item_skip_code','4');
16283 +            if (type in ('LATTICE') and desc in ('ASSESSRANKOVERLAY'))
16284 +                then call symput('item_skip_code','5');
16286 +            if (type in ('PLOT') and desc in ('EVENTPLOT'))
16287 +                then call symput('item_skip_code','6');
16289 +            if (type in ('LATTICE') and desc in ('CLASSSTAT'))
16290 +                then call symput('EM_REPORT_KEY_VIEWTYPE','DATA');
16292 +            if (type in ('LATTICE') and desc in ('ASSESSSCOREDIST'))
16293 +                then call symput('item_skip_code','7');
16294 +            if (type in ('LATTICE') and desc in ('SCOREDIST'))
16295 +                then call symput('item_skip_code','8');
16296 +            if (type in ('LATTICE') and desc in ('CLASSIFICATIONMANY'))
16297 +                then call symput('item_skip_code','9');
16299 +            if (type in ('PLOT') and desc in ('HPDMASSESS'))
16300 +                then call symput('item_skip_code','10');
16302 +            if (type in ('HISTOGRAM') and desc in ('HPWEIGHTS' 'BELIEFS BY VALUE' 'TSDISTANCEMAP'))
16303 +                then call symput('item_skip_code','11');
16305 +        run;
16307 +        /* 7.3.5 */
16309 +        %EM_REPORT_GTT;
16311 +        /********** actions by item_skip_code ***********************************/
16313 +        %if &item_skip_code eq 2 %then %do;
16314 +            /* TLF is skipped for these types of reports */
16315 +        %end;
16316 +        %else
16317 +        %if &item_skip_code eq 3 %then %do;
16318 +            %if (("&EM_REPORT_KEY_AUTODISPLAY" eq "Y") OR ("&EM_REPORT_SHOWALL" eq "Y")) %then %do;
16319 +                /* just a print */
16320 +                title1 "&EM_REPORT_TTL1";
16321 +                title2 "&EM_REPORT_TTL2";
16322 +                title3 "&EM_REPORT_TTL3";
16323 +                ods proclabel = "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text))) %sysfunc(sasmsg(sashelp.dmine, views_descriptions_&em_report_key_description, NOQUOTE))";
16324 +                proc print data=&EM_REPORT_LIB..&EM_REPORT_KEY_FILE noobs label contents='';
16325 +                run;
16326 +            %end;
16327 +        %end;
16328 +        %else
16329 +        %if &item_skip_code eq 4 %then %do;
16330 +            %EM_REPORT_EMRANK(pdat=&em_report_lib..&EM_REPORT_KEY_FILE,lattice=no);
16331 +        %end;
16332 +        %else
16333 +        %if &item_skip_code eq 5 %then %do;
16334 +            %EM_REPORT_EMRANK(pdat=&em_report_lib..&EM_REPORT_KEY_FILE,lattice=yes);
16335 +        %end;
16336 +        %else
16337 +        %if &item_skip_code eq 6 %then %do;
16338 +            %EM_REPORT_EVENTPLOT;
16339 +        %end;
16340 +        %else
16341 +        %if &item_skip_code eq 7 %then %do;
16342 +            %if %substr(%upcase(%sysfunc(strip(&nodeid))),1,7) ne MDLCOMP or &EM_REPORT_NODES eq PREDECESSOR %then %do;
16343 +                %EM_REPORT_SCOREDIST;
16344 +            %end;
16345 +        %end;
16346 +        %else
16347 +        %if &item_skip_code eq 8 %then %do;
16348 +            %EM_REPORT_SCOREDIST2;
16349 +        %end;
16350 +        %else
16351 +        %if &item_skip_code eq 9 %then %do;
16352 +            %if (("&EM_REPORT_KEY_AUTODISPLAY" eq "Y") OR ("&EM_REPORT_SHOWALL" eq "Y")) %then %do;
16353 +                %EM_REPORT_CLASSIFYTAB;
16354 +            %end;
16355 +        %end;
16356 +        %else
16357 +        %if &item_skip_code eq 10 %then %do;
16358 +            %if (("&EM_REPORT_KEY_AUTODISPLAY" eq "Y") OR ("&EM_REPORT_SHOWALL" eq "Y")) %then %do;
16359 +                %EM_REPORT_HPDMASSESS;
16360 +            %end;
16361 +        %end;
16362 +        %else
16363 +        %if &item_skip_code eq 11 %then %do;
16364 +                %EM_REPORT_HEATMAPTEMPLATE;
16365 +        %end;
16368 +        %else
16370 +        %if &item_skip_code eq 1 %then %do;
16372 +            /*--- process the viewtypes ---*/
16373 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "PLOT"           %then %do; %EM_REPORT_SGPLOT(type=PLOT);     %end;
16374 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "LINEPLOT"       %then %do; %EM_REPORT_SGPLOT(type=LINEPLOT); %end;
16375 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "SCATTER"        %then %do; %EM_REPORT_SGPLOT(type=SCATTER);  %end;
16376 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "BAR"            %then %do; %EM_REPORT_SGPLOT(type=BARPLOT);  %end;
16377 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "HISTOGRAM"      %then %do; %EM_REPORT_SGPLOT(type=HISTOGRAM);%end;
16378 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "PIE"            %then %do; %EM_REPORT_SGSILK(type=PIE);      %end;
16379 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "DTREE"          %then %do; %EM_REPORT_SGSILK(type=DTREE);    %end;
16380 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "ICICLE"         %then %do; %EM_REPORT_SGSILK(type=ICICLE);   %end;
16381 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "DENDROGRAM"     %then %do; %EM_REPORT_DENDROGRAM;            %end;
16382 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "ITERATIONPLOT"  %then %do; %EM_REPORT_ITERATIONPLOT;         %end;
16383 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "DATA"           %then %do; %EM_REPORT_DATA(maxobs=100);      %end;
16384 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "MULTIPLEY2PLOT" %then %do; %EM_REPORT_MULTIPLY2PLOT;         %end;
16386 +            %if "&EM_REPORT_KEY_VIEWTYPE" eq "LATTICE" %then %do;
16387 +                ods graphics / height=9in;
16388 +                %EM_REPORT_LATTICE;
16389 +                ods graphics / height=4.5in;
16390 +            %end;
16391 +        %end;
16393 +        title1; title2; title3; title4; title5;
16394 +    %end;
16396 +%node_skip:
16398 +%mend EM_REPORT_NODE;
16400 +/*---------------------------------------------------------*/
16401 +/*---- create xml file containing pfd information      ----*/
16402 +/*---------------------------------------------------------*/
16403 +%macro em_report_workspace_xml();
16405 +    %global EM_REPORT_LIB;
16407 +    %EM_REGISTER(key=PFD, type=FILE, extension=XML);
16408 +    %EM_GETNAME(key=PFD,  type=FILE, extension=XML);
16410 +    filename x "&EM_USER_PFD" encoding="utf-8" NOBOM;
16412 +    /* Node information */
16413 +    data _null_;
16414 +        file x;
16415 +        set &EM_REPORT_LIB..EM_NODEID;
16416 +        length string $400;
16418 +        /* check for invalid characters in the node label */
16419 +        label = tranwrd(label, %nrstr("&"), %nrstr("&amp;"));
16420 +        label = tranwrd(label, %nrstr("<"), %nrstr("&lt;"));
16421 +        label = tranwrd(label, %nrstr(">"), %nrstr("&gt;"));
16422 +        label = tranwrd(label, %nrstr("'"), %nrstr("&#39;"));
16423 +        label = tranwrd(label, %nrstr('"'), %nrstr('&quot;'));
16425 +        string = cats('<PFDNode x="',X,'" y="',Y,'" id="',NODEID,'" title="',LABEL,'"></PFDNode>');
16426 +        put string;
16427 +    run;
16429 +    /* Link information */
16430 +    data _null_;
16431 +        file x mod;
16432 +        set &EM_REPORT_LIB..EM_DGRAPH(where=(TO ne ""));
16433 +        length string $400;
16435 +        string = cats("<PFDLink id='",_N_,"' fromId='",FROM,"' toId='",TO,"'></PFDLink>");
16436 +        put string;
16437 +    run;
16439 +    filename x;
16441 +%mend em_report_workspace_xml;
16443 +/*---------------------------------------------------------*/
16444 +/* "chop" xml definitions, wrapping if over 256 bytes      */
16445 +/*---------------------------------------------------------*/
16446 +%macro em_report_makeds(XMLfile,outds);
16448 +    %local rc fileref fid max reclen;
16449 +    %let fileref=xmlref;
16450 +    %if %sysfunc(fexist(xmlref)) %then %put ERROR: Fileref XMLREF in use.;
16451 +    %else %do;
16452 +      %let rc=%sysfunc(filename(fileref,&XMLfile,,encoding='utf-8')));
16453 +      %if %sysfunc(fileref(xmlref)) ne 0 %then %put %sysfunc(sysmsg());
16454 +      %else %do;
16455 +        %let fid=%sysfunc(fopen(xmlref,i,5000,v));
16456 +        %let max=0;
16457 +         %if %sysfunc(fread(&fid)) = 0 %then %do;
16458 +            %let reclen=%sysfunc(frlen(&fid));
16459 +            %let max=&reclen;
16460 +            %do %while(%sysfunc(fread(&fid)) = 0);
16461 +               %let reclen=%sysfunc(frlen(&fid));
16462 +               %if &reclen > &max %then %let max=&reclen;
16463 +            %end;
16464 +        %end;
16465 +        %let rc=%sysfunc(fclose(&fid));
16467 +        data &outds(keep=PFDItemList);
16468 +          infile xmlref recfm=v  lrecl=&max  length=len;
16469 +          length PFDItemList $256;
16470 +          input record $varying5000. len;
16471 +          recordNum+1;
16472 +          do start=1 to len by 256 ;
16473 +             end=start+256;
16474 +             *put recordNum= start= end=;
16475 +             PFDItemList=substr(record,start,end);
16476 +             output;
16477 +          end;
16478 +        run;
16479 +        %let rc=%sysfunc(filename(fileref));
16480 +      %end;
16481 +    %end;
16482 +%mend em_report_makeds;
16484 +/*---------------------------------------------------------*/
16485 +/* render a workspace plot in the ODS destination          */
16486 +/*---------------------------------------------------------*/
16487 +%macro em_report_workspace_render;
16489 +     /*--- determine if flow was run in batch (ie all x/y coordinates are -1) ---*/
16490 +     %let runBatch = 0;
16491 +     data temp;
16492 +        set &EM_REPORT_LIB..EM_NODEID;
16493 +        if X = -1 AND Y= - 1 then delete;
16494 +     run;
16495 +     %let dsid = %sysfunc(open(work.temp));
16496 +     %let nobs = %sysfunc(ATTRN(&dsid, NOBS));
16497 +     %if &dsid %then %let dsid = %sysfunc(close(&dsid));
16499 +     %if &nobs le 1 %then %let runBatch = 1;
16501 +      title1; title2; title3;
16503 +      %let temp = %nrbquote(%sysfunc(PATHNAME(&em_report_lib)));
16504 +      %let path = %nrbquote(%sysfunc(tranwrd(&temp, %str(%"),"")));
16505 +      %let view = view.xml;
16506 +      %let viewfile = %nrbquote(&path&em_dsep&EM_NODEID&em_dsep&view);
16507 +      %let datax = data.xml;
16508 +      %let dataxfile = %nrbquote(&path&em_dsep&EM_NODEID&em_dsep&datax);
16510 +      /* make sgDataModel*/
16511 +      ods &EM_REPORT_FORMAT exclude SGRENDER; /* disable sgrender output to PDF */
16512 +      ods tagsets.gtl file="&viewfile" data="&dataxfile" encoding="utf-8" style=styles.em_style;
16513 +      proc sgrender data=work.xmldata template=dataonly;
16514 +          dynamic var1="PFDItemList";
16515 +      run;
16517 +      /* pfd event code */
16518 +      %if "&EM_REPORT_TITLE" ne "" %then %do;
16519 +          ods tagsets.gtl event=LayoutRegion(start)
16520 +              options(title ="&EM_REPORT_TITLE"
16521 +                      title2="%sysfunc(sasmsg(sashelp.dmine, rpt_processflow_title, NOQUOTE))");
16522 +      %end;
16523 +      %else %do;
16524 +          ods tagsets.gtl event=LayoutRegion(start)
16525 +              options(title ="%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))"
16526 +                      title2="%sysfunc(sasmsg(sashelp.dmine, rpt_processflow_title, NOQUOTE))");
16527 +      %end;
16529 +      %if &runBatch ne 1 %then %do;
16530 +          ods tagsets.gtl event=PFD options(pfditems="PFDItemList" id="pfdView1");
16531 +          ods tagsets.gtl event=LayoutRegion(finish);
16532 +          ods tagsets.gtl close;
16533 +          ods &EM_REPORT_FORMAT select ALL ; /* re-enable sgrender output */
16534 +          ods proclabel = "%sysfunc(sasmsg(sashelp.dmine, rpt_processflow_title, NOQUOTE))";
16535 +          proc sgsilk viewxml="&viewfile" dataxml="&dataxfile"; run;
16536 +      %end;
16537 +      title1; title2; title3;
16539 +%mend em_report_workspace_render;
16542 +%macro EM_REPORT_RESULTS(
16543 +    path=,              /*-- output location             ---*/
16544 +    lib=,               /*-- em workspace library        ---*/
16545 +    startnodeid=,       /*-- starting nodeid             ---*/
16546 +    showall=,           /*-- show all content Y or N     ---*/
16547 +    nodes=,             /*-- nodes to generate report on ---*/
16548 +    format=,            /*-- format for generated report ---*/
16549 +    style=              /*--- style for ODS statement    ---*/
16550 +) ;
16552 +    data _null_ ;
16553 +        call symput('report_date', strip(put(date(),     NLDATE.)));
16554 +        call symput('report_time', %nrbquote(strip(put(datetime(), NLTIME.))));
16555 +    run;
16557 +    data _null_;
16558 +        file print ;
16559 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_user_note, NOQUOTE, &sysuserid))" ;
16560 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_date_note, NOQUOTE, %nrbquote(&report_time), &report_date))" ;
16561 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_project_note, NOQUOTE, &EM_REPORT_PROJECT_TEXT)) " ;
16562 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_diagram_note, NOQUOTE, %nrstr(&EM_REPORT_DIAGRAM_TEXT)))" / ;
16563 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_startnode_note, NOQUOTE, &startnodeid))";
16564 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_nodelabel_note, NOQUOTE, %nrbquote(&em_report_node_text)))" ;
16565 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_nodes_note, NOQUOTE, &EM_REPORT_NODES))" ;
16566 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_showall_note, NOQUOTE, &EM_REPORT_SHOWALL))" / ;
16567 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_format_note, NOQUOTE, &EM_REPORT_FORMAT))" ;
16568 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_graphics_note, NOQUOTE, &EM_REPORT_GDEVICE))" ;
16569 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_style_note, NOQUOTE, &STYLE))" / ;
16570 +    run ;
16572 +    /* add report view to results */
16573 +    %EM_GETNAME(key=REPORT, type=FILE, extension=&FORMAT);
16574 +    %EM_REPORT(KEY=REPORT, BLOCK=MODEL, VIEWTYPE = FILEVIEWER, autodisplay=Y, DESCRIPTION=CUSTOM);
16576 +%mend;
16578 +%macro EM_REPORT_CREATERESULTS();
16580 +    %EM_GETNAME(key=ODSRESULTS, type=FILE, extension=htm, folder=ODS);
16581 +    filename x "&EM_FILE_ODSRESULTS" nobom;
16583 +    ods listing close;
16584 +    ods html3 file=X encoding = "utf-8" style=styles.em_style;
16585 +    ods html3 text=" ";
16586 +    %if "&EM_REPORT_TITLE" ne "" %then %do;
16587 +        ods html3 text="&EM_REPORT_TITLE";
16588 +    %end;
16589 +    %else %do;
16590 +        ods html3 text="%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))";
16591 +    %end;
16593 +    data _null_ ;
16594 +        call symput('report_date', strip(put(date(),     NLDATE.)));
16595 +        call symput('report_time', %nrbquote(strip(put(datetime(), NLTIME.))));
16596 +    run;
16598 +    data _null_;
16599 +        file print ;
16600 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_user_note, NOQUOTE, &sysuserid))" ;
16601 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_date_note, NOQUOTE, %nrbquote(&report_time), &report_date))" ;
16602 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_project_note, NOQUOTE, &EM_REPORT_PROJECT_TEXT)) " ;
16603 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_diagram_note, NOQUOTE, %nrstr(&EM_REPORT_DIAGRAM_TEXT)))" / ;
16604 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_startnode_note, NOQUOTE, &startnodeid))";
16605 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_nodelabel_note, NOQUOTE, %nrbquote(&em_report_node_text)))" ;
16606 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_nodes_note, NOQUOTE, &EM_REPORT_NODES))" ;
16607 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_showall_note, NOQUOTE, &showall))" / ;
16608 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_format_note, NOQUOTE, &EM_REPORT_FORMAT))" ;
16609 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_graphics_note, NOQUOTE, &EM_REPORT_GDEVICE))" ;
16610 +        put "%sysfunc(sasmsg(sashelp.dmine, rpt_style_note, NOQUOTE, &EM_REPORT_STYLE))" / ;
16611 +    run ;
16613 +    filename x;
16614 +    ods listing;
16615 +    ods html3 close;
16617 +    %EM_REPORT(KEY=ODSRESULTS, BLOCK=MODEL, VIEWTYPE = HTMLVIEW);
16619 +%mend EM_REPORT_CREATERESULTS;
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMRPM.EM_MODELPROPS.SOURCE.
16620 +%macro em_modelprops(dgmid=, modelid=, output=%nrbquote(work.properties));
16621 +  %global mdl_emwsid mdl_modelid;
16622 +  %do;
16623 +     %let mdl_emwsid  = &dgmid;
16624 +     %let mdl_modelid = &modelid;
16625 +     %let mdl_output  = &output;
16626 +
16627 +     dm "afa c=sashelp.emrpm.modelprops.scl";
16628 +  %end;
16629 +%mend em_modelprops;
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
NOTE: Fileref PDFFILE has been deassigned.
NOTE: Fileref RTFFILE has been deassigned.
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMUTIL.REPORTER_PLOTMACROS.SOURCE.
16630 +/*---------------------------------------------------------*/
16631 + * Utility functions:;
16632 +/*---------------------------------------------------------*/
16634 +%macro EM_REPORT_GTT();
16636 +    %global EM_REPORT_TTL1 EM_REPORT_TTL2 EM_REPORT_TTL3 EM_REPORT_TITLE EM_REPORT_NODE_TEXT EM_REPORT_KEY_description;
16638 +    %if "&EM_REPORT_TITLE" ne "" %then %do;
16639 +        %let EM_REPORT_ttl1 = &EM_REPORT_TITLE;
16640 +    %end;
16641 +    %else %do;
16642 +        %let EM_REPORT_ttl1 = %sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE));
16643 +    %end;
16645 +    %let EM_REPORT_ttl2 = %sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text)));
16647 +    %let msg0 = views_descriptions_&em_report_key_description;
16648 +    %let msg1 = %sysfunc(sasmsg(sashelp.dmine, views_descriptions_&em_report_key_description, NOQUOTE));
16649 +    %if %sysfunc(exist(sashelp.tmine)) %then %do;
16650 +        %let msg2 = %sysfunc(sasmsg(sashelp.tmine, views_descriptions_&em_report_key_description, NOQUOTE));
16651 +    %end;
16652 +    %else %do;
16653 +        %let msg2=views_descriptions_&em_report_key_description;
16654 +    %end;
16656 +    %if "%sysfunc(strip(%nrstr(&msg1)))" ne "%sysfunc(strip(&msg0))"
16657 +    %then %do;
16658 +        %let EM_REPORT_ttl3 = %sysfunc(sasmsg(sashelp.dmine, views_descriptions_&em_report_key_description, NOQUOTE));
16659 +    %end;
16660 +    %else %if "%sysfunc(strip(%nrstr(&msg2)))" ne "%sysfunc(strip(&msg0))"
16661 +    %then %do;
16662 +        %if %sysfunc(exist(sashelp.tmine)) %then %do;
16663 +            %let EM_REPORT_ttl3 = %sysfunc(sasmsg(sashelp.tmine, views_descriptions_&em_report_key_description, NOQUOTE));
16664 +        %end;
16665 +    %end;
16666 +    %else %do;
16667 +        %let EM_REPORT_ttl3 = &em_report_key_description;
16668 +    %end;
16670 +    /*--- special labels ---*/
16672 +    %if "&EM_REPORT_KEY_DESCRIPTION" eq "REGEFFECTS"
16673 +    %then %do;
16674 +       %let EM_REPORT_ttl3 = %sysfunc(sasmsg(sashelp.dmine, rpt_regressionmod_title, NOQUOTE));
16675 +    %end;
16677 +%mend;
16679 +/*---------------------------------------------------------*/
16680 +* Plots created by SGPLOT: LINEPLOT, SCATTER, PLOT, BAR;
16681 +/*---------------------------------------------------------*/
16683 +%macro EM_REPORT_SGPLOT(type=);
16685 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
16687 +    title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
16689 +    /*--- EM_REPORT_SGPLOT 1. identify the plot statement ---*/
16691 +    %let axisstmt = xaxis;
16693 +    %if &type eq LINEPLOT   %then %let plotstmt = series;
16694 +    %if &type eq PLOT       %then %let plotstmt = series;
16695 +    %if &type eq SCATTER    %then %let plotstmt = scatter;
16696 +    %if &type eq BARPLOT    %then %let plotstmt = vbar;
16697 +    %if &type eq HISTOGRAM  %then %let plotstmt = histogram;
16699 +    %if "&plotstmt" eq "vbar" and "%upcase(&EM_REPORT_KEY_ORIENTATION)" eq "HORIZONTAL"
16700 +    %then %do;
16701 +        %let plotstmt = hbar;
16702 +        %let axisstmt = yaxis;
16703 +    %end;
16704 +    /*--- EM_REPORT_SGPLOT 2. options for plot statements ---*/
16706 +    %let plotopt = ;
16708 +    %if &type eq LINEPLOT %then %do;
16709 +        %if "&EM_REPORT_KEY_GROUP" ne "" %then %let plotopt = %str(&plotopt group = &EM_REPORT_KEY_GROUP);
16710 +    %end;
16711 +    %else %if &type eq PLOT %then %do;
16712 +        %if "&EM_REPORT_KEY_GROUP" ne "" %then %let plotopt = %str(&plotopt group = &EM_REPORT_KEY_GROUP);
16713 +    %end;
16714 +    %else %if &type eq SCATTER %then %do;
16715 +        %if "&EM_REPORT_KEY_GROUP" ne ""     %then %let plotopt = %str(&plotopt group = &EM_REPORT_KEY_GROUP);
16716 +        %if "&EM_REPORT_KEY_FREQUENCY" ne "" %then %let plotopt = %str(&plotopt freq=&EM_REPORT_KEY_FREQUENCY);
16717 +        %let plotopt = %str(&plotopt markerattrs=(symbol=squarefilled));
16718 +    %end;
16719 +    %else %if &type eq BARPLOT %then %do;
16720 +        %let plotopt = missing;
16721 +        %if "&EM_REPORT_KEY_FREQUENCY" ne "" %then %let plotopt = %str(&plotopt response=&EM_REPORT_KEY_FREQUENCY);
16722 +        %if "&EM_REPORT_KEY_SUBGROUP" ne ""  %then %let plotopt = %str(&plotopt group=&EM_REPORT_KEY_SUBGROUP);
16723 +        %else %if "&EM_REPORT_KEY_GROUP" ne ""  %then %let plotopt = %str(&plotopt group=&EM_REPORT_KEY_GROUP);
16724 +    %end;
16725 +    %else %if &type eq HISTOGRAM %then %do;
16726 +        %if "&EM_REPORT_KEY_FREQUENCY" ne "" %then %let plotopt = %str(&plotopt freq=&EM_REPORT_KEY_FREQUENCY);
16727 +        %if "&EM_REPORT_KEY_SUBGROUP" ne ""  %then %let plotopt = %str(&plotopt group=&EM_REPORT_KEY_SUBGROUP);
16728 +/*        %if "&EM_REPORT_KEY_Y" ne ""         %then %let plotopt = %str(&plotopt group=&EM_REPORT_KEY_Y);*/
16729 +    %end;
16731 +    %if "&plotopt" ne "" %then %let plotopt = %str(/ &plotopt);
16733 +    /*--- EM_REPORT_SGPLOT 3. handle _ANYNUMERIC_ or BYSET---*/
16735 +    %if &type eq PLOT and "&EM_REPORT_KEY_Y" eq "_ANYNUMERIC_" %then %do;
16737 +         proc contents data=&pdat out=_rptsgplot_temp(keep=name type) noprint;
16738 +         run;
16740 +         /*** For _ANYNUMERIC_, get the first numeric variable that is not X ***/
16741 +         data _null_;
16742 +            set _rptsgplot_temp(where=(type eq 1 and upcase(name) ne "%upcase(&EM_REPORT_KEY_X)"));
16743 +            if _n_=1 then call symput("EM_REPORT_KEY_Y", strip(name));
16744 +         run;
16745 +    %end;
16747 +    /*--- EM_REPORT_SGPLOT 4. call SGPLOT ---*/
16749 +    ods graphics / discretemax=5000;
16751 +    proc sgplot data=&pdat.&EM_REPORT_KEY_WHERECLAUSE;
16753 +        %if "&EM_REPORT_KEY_Y" ne "" and "&type" ne "BARPLOT" and "&type" ne "HISTOGRAM" %then %do;
16754 +            %let i = 1;
16755 +            %do %while ("%scan(&EM_REPORT_KEY_Y,&i,' ')" ne "");
16756 +                %let thisY=%scan(&EM_REPORT_KEY_Y,&i,' ');
16757 +                %let i = %eval(&i+1);
16758 +                &plotstmt
16759 +                    x = &EM_REPORT_KEY_X  y = &thisY  &plotopt
16760 +                ;
16761 +            %end;
16762 +        %end;
16763 +        %if "&EM_REPORT_KEY_Y2" ne "" and "&type" ne "BARPLOT" and "&type" ne "HISTOGRAM" %then %do;
16764 +            &plotstmt
16765 +                x = &EM_REPORT_KEY_X  y = &EM_REPORT_KEY_Y2  &plotopt
16766 +            ;
16767 +        %end;
16768 +        %if "&type" eq "BARPLOT" %then %do;
16769 +            &plotstmt
16770 +                &EM_REPORT_KEY_X &plotopt;
16771 +            &axisstmt
16772 +                type=discrete /* discreteorder=data */
16773 +                %if "&EM_REPORT_KEY_DESCRIPTION" eq "REGEFFECTS" %then %do; display=(NOVALUES NOTICKS) %end;
16774 +            ;
16775 +        %end;
16776 +        %if "&type" eq "HISTOGRAM" and "&EM_REPORT_KEY_FREQUENCY" ne "" %then %do;
16777 +            &plotstmt
16778 +                &EM_REPORT_KEY_X &plotopt;
16779 +            &axisstmt
16780 +                %if "&EM_REPORT_KEY_DESCRIPTION" eq "REGEFFECTS" %then %do; display=(NOVALUES NOTICKS) %end;
16781 +            ;
16782 +        %end;
16783 +        %if "&type" eq "HISTOGRAM" and "&EM_REPORT_KEY_FREQUENCY" eq "" %then %do;
16784 +            &plotstmt &EM_REPORT_KEY_X;
16785 +        %end;
16786 +        %if "&type" ne "BARPLOT" and "&type" ne "HISTOGRAM" %then %do;
16787 +            %if "&EM_REPORT_KEY_YREF" ne "" %then %do; refline &EM_REPORT_KEY_YREF / axis = y; %end;
16788 +            %if "&EM_REPORT_KEY_XREF" ne "" %then %do; refline &EM_REPORT_KEY_XREF / axis = x; %end;
16789 +        %end;
16791 +        keylegend;
16792 +    run; quit;
16794 +    %if "&EM_REPORT_KEY_DESCRIPTION" eq "REGEFFECTS" %then %do;
16795 +        %EM_REPORT_makeMultiColumn(dat=&pdat.&EM_REPORT_KEY_WHERECLAUSE, vars=effectnum variable classlevel coefficient tvalue pvalue, split=2);
16796 +    %end;
16798 +    ods graphics / discretemax=100;
16799 +    title1; title2; title3;
16801 +%mend EM_REPORT_SGPLOT;
16803 +/*---------------------------------------------------------*/
16804 +* Plots rendered by SGSILK: DTREE, ICICLE, PIE;
16805 +/*---------------------------------------------------------*/
16807 +%macro EM_REPORT_SGSILK(type=);
16809 +    %local temp path view datax viewfile dataxfile;
16810 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
16812 +    /*--- EM_REPORT_SGSILK 1. define the XML files for view and data ---*/
16814 +    %let temp      = %nrbquote(%sysfunc(PATHNAME(&EM_REPORT_LIB)));
16815 +    %let path      = %nrbquote(%sysfunc(tranwrd(&temp, %str(%"),"")));
16816 +    %let view      = view.xml;
16817 +    %let datax     = data.xml;
16818 +    %let viewfile  = %nrbquote(&path&em_dsep&EM_NODEID&em_dsep&view);
16819 +    %let dataxfile = %nrbquote(&path&em_dsep&EM_NODEID&em_dsep&datax);
16821 +    /*--- EM_REPORT_SGSILK 2. Call SGRENGER and setup GTL tagsets---*/
16823 +    ods &EM_REPORT_FORMAT exclude SGRENDER; /* disable sgrender output to PDF */
16824 +    ods tagsets.gtl file="&viewfile" data="&dataxfile" encoding="utf-8" style=styles.em_style;
16826 +    %if &type eq DTREE %then %do;   /****** DTREE ******/
16828 +        proc sgrender data=&pdat.&EM_REPORT_KEY_WHERECLAUSE template=dataonly;
16829 +            dynamic var1 = "&EM_REPORT_KEY_ID";
16830 +            dynamic var2 = "&EM_REPORT_KEY_PARENT";
16831 +            dynamic var3 = "&EM_REPORT_KEY_NODETEXT";
16832 +            dynamic var4 = "&EM_REPORT_KEY_NODECOLOR";
16833 +            dynamic var5 = "&EM_REPORT_KEY_ABOVETEXT";
16834 +            dynamic var6 = "&EM_REPORT_KEY_BELOWTEXT";
16835 +            dynamic var7 = "&EM_REPORT_KEY_LINKWIDTH";
16836 +        run;
16838 +        ods tagsets.gtl event=LayoutRegion(start)
16839 +            options(title="&EM_REPORT_TTL1" title2="&EM_REPORT_TTL2" title3="&EM_REPORT_TTL3");
16841 +        ods tagsets.gtl event=DecisionTree
16842 +            options( id        = "&EM_REPORT_KEY_ID"
16843 +                     parent    = "&EM_REPORT_KEY_PARENT"
16844 +                     nodetext  = "&EM_REPORT_KEY_NODETEXT"
16845 +                     abovetext = "&EM_REPORT_KEY_ABOVETEXT"
16846 +                     belowtext = "&EM_REPORT_KEY_BELOWTEXT"
16847 +                     linkwidth = "&EM_REPORT_KEY_LINKWIDTH"
16848 +                     nodecolor = "&EM_REPORT_KEY_NODECOLOR" );
16850 +    %end;
16851 +    %else %if &type eq ICICLE %then %do;      /****** ICICLE ******/
16853 +        proc sgrender data=&pdat.&EM_REPORT_KEY_WHERECLAUSE template=dataonly;
16854 +            dynamic var1="&EM_REPORT_KEY_ID";
16855 +            dynamic var2="&EM_REPORT_KEY_PARENT";
16856 +            dynamic var3="&EM_REPORT_KEY_NODECOLOR";
16857 +            dynamic var4="&EM_REPORT_KEY_NODESIZE";
16858 +        run;
16860 +        ods tagsets.gtl event=LayoutRegion(start)
16861 +            options(title="&EM_REPORT_TTL1" title2="&EM_REPORT_TTL2" title3="&EM_REPORT_TTL3");
16863 +        ods tagsets.gtl event=Icicle
16864 +            options( id        = "&EM_REPORT_KEY_ID"
16865 +                     parent    = "&EM_REPORT_KEY_PARENT"
16866 +                     nodecolor = "&EM_REPORT_KEY_NODECOLOR"
16867 +                     nodesize  = "&EM_REPORT_KEY_NODESIZE" );
16869 +    %end;
16870 +    %else %if &type eq PIE %then %do;      /****** PIE ******/
16872 +      proc sgrender data=&pdat.&EM_REPORT_KEY_WHERECLAUSE template=dataonly;
16873 +          dynamic var1="&EM_REPORT_KEY_X"
16874 +                  var2=
16875 +                       %if &EM_REPORT_KEY_Y NE %str() %then "&EM_REPORT_KEY_Y" ;
16876 +                       %else "&EM_REPORT_KEY_FREQUENCY";
16877 +               ;
16878 +      run;
16879 +      quit;
16881 +      ods tagsets.gtl event=LayoutRegion(start)
16882 +          options(title="&EM_REPORT_TTL1" title2="&EM_REPORT_TTL2" title3="&EM_REPORT_TTL3");
16884 +      ods tagsets.gtl event=Pie
16885 +          options( category = "&EM_REPORT_KEY_X"
16886 +                   response =
16887 +                             %if &EM_REPORT_KEY_Y NE %str() %then "&EM_REPORT_KEY_Y" ;
16888 +                             %else "&EM_REPORT_KEY_FREQUENCY";
16889 +                   pielabeldisplay = "1"         );
16892 +    %end;
16894 +    ods tagsets.gtl event=LayoutRegion(finish);
16895 +    ods tagsets.gtl close;
16897 +    /*--- EM_REPORT_SGSILK 3. Call SGSILK to render the graph---*/
16899 +    ods &EM_REPORT_FORMAT select ALL; /* re-enable sgrender output */
16900 +    proc sgsilk viewxml="&viewfile" dataxml="&dataxfile"; run;
16901 +    title1; title2; title3;
16902 +%mend;
16904 +/*---------------------------------------------------------*/
16905 +* DENDROGRAM <- PROC TREE;
16906 +/*---------------------------------------------------------*/
16908 +%macro EM_REPORT_DENDROGRAM() ;
16910 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
16912 +    *goptions device=&EM_REPORT_GDEVICE ;
16913 +    goptions reset=all device=&EM_REPORT_GDEVICE;
16915 +    title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
16917 +    axis1 minor=none label=(height = &EM_REPORT_TEXT_SIZE pt ) ;
16918 +    axis2 minor=none label=(height = &EM_REPORT_TEXT_SIZE pt angle=90 ) ;
16920 +    ods &EM_REPORT_FORMAT select ALL; /* re-enable sgrender output */
16922 +    proc tree data=&pdat.&EM_REPORT_KEY_WHERECLAUSE horizontal haxis=axis1 vaxis=axis2;
16923 +        height &EM_REPORT_KEY_HEIGHT;
16924 +        name   &EM_REPORT_KEY_NAME;
16925 +        parent &EM_REPORT_KEY_PARENT;
16926 +    run; quit;
16928 +    title1; title2; title3 ;
16929 +    axis1; axis2;
16931 +%mend EM_REPORT_DENDROGRAM;
16933 +/*---------------------------------------------------------*/
16934 +* EVENTPLOT <- SGSCATTER;
16935 +/*---------------------------------------------------------*/
16937 +%macro EM_REPORT_EVENTPLOT();
16939 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
16941 +    title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
16943 +    proc sgscatter data=&pdat.&EM_REPORT_KEY_WHERECLAUSE;
16944 +        plot (_cumulative_event_count_ _marginal_event_rate_ _event_count_ _cumulative_event_rate_)
16945 +            * _cutoff_score_ / group=datarole join=() markerattrs=(size=0) grid;
16946 +    run; quit;
16948 +    title1; title2; title3 ;
16950 +%mend EM_REPORT_EVENTPLOT;
16952 +/*---------------------------------------------------------*/
16953 + * EM_REPORT_ITERATIONPLOT ;
16954 + * create plots of iterations: neural, stepwise, tree, etc..;
16955 +/*---------------------------------------------------------*/
16957 +%macro EM_REPORT_ITERATIONPLOT();
16959 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
16960 +    %let X    = &EM_REPORT_KEY_X;
16962 +    title1 "&EM_REPORT_TTL1";
16963 +    title2 "&EM_REPORT_TTL2";
16964 +    title3 "%sysfunc(sasmsg(sashelp.dmine, rpt_mdliterationplot_title, NOQUOTE))";
16966 +    %local _misc_ _vmisc_ _ase_ _vase_ ;
16967 +    data _null_ ;
16968 +        set &pdat(obs=2) end=eof;
16969 +        if eof then do ;
16970 +            call symput('_ase_',_ase_);
16971 +            call symput('_vase_',_vase_);
16972 +            call symput('_misc_',_misc_);
16973 +            call symput('_vmisc_',_vmisc_);
16974 +        end;
16975 +    run;
16977 +    %let dsid = %sysfunc(open(&pdat));
16978 +    %if &dsid %then %do;
16979 +        %let _ase_ = %sysfunc(VARNUM(&dsid, _ase_));
16980 +        %let _vase_ = %sysfunc(VARNUM(&dsid, _vase_));
16981 +        %let _misc_ = %sysfunc(VARNUM(&dsid, _misc_));
16982 +        %let _vmisc_ = %sysfunc(VARNUM(&dsid, _vmisc_));
16983 +    %end;
16984 +    %if &dsid %then %let dsid = %sysfunc(close(&dsid));
16986 +    %if "&_ase_"   eq "." %then %let _ase_   = 0;
16987 +    %if "&_vase_"  eq "." %then %let _vase_  = 0;
16988 +    %if "&_misc_"  eq "." %then %let _misc_  = 0;
16989 +    %if "&_vmisc_" eq "." %then %let _vmisc_ = 0;
16991 +    %put &_ase_ &_vase_ &_misc_ &_vmisc_;
16993 +    %if &_vmisc_ ne 0 %then %do;
16994 +        proc sql noprint;
16995 +            select count(*) into :nonMiss_vmisc from &pdat where _vmisc_ ne .;
16996 +        quit;
16997 +    %end;
16998 +    %else %do;
16999 +        %let nonMiss_vmisc = 0;
17000 +    %end;
17002 +    ods path(prepend) work.templat(update);
17003 +    proc template;
17004 +        define statgraph em.IterationPlot / store=work.templat;
17005 +            begingraph;
17006 +                entrytitle "&EM_REPORT_TTL1";
17007 +                entrytitle "&EM_REPORT_TTL2";
17008 +                entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_mdliterationplot_title, NOQUOTE))";
17010 +              layout lattice / order=columnmajor rows=1 rowdatarange=data columngutter=10;
17012 +                %if &_ase_ ne 0 or &_vase_ ne 0 %then %do;
17013 +                    layout overlay / cycleattrs=true
17014 +                        xaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, rpt_iteration_vlabel, NOQUOTE))")
17015 +                        yaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, stat_ase_vlabel, NOQUOTE))");
17016 +                        %if &_ase_ ne 0 %then %do ;
17017 +                            seriesplot x=&X y=_ASE_ /  markerattrs=(size=0) display=all name='train'
17018 +                                legendlabel="%sysfunc(sasmsg(sashelp.dmine, rpt_roletrain_value, NOQUOTE))";
17019 +                        %end;
17020 +                        %if &_vase_  ne 0 %then %do ;
17021 +                            seriesplot x=&X y=_VASE_ /  markerattrs=(size=0) display=all name='validate'
17022 +                                legendlabel="%sysfunc(sasmsg(sashelp.dmine, rpt_rolevalidate_value, NOQUOTE))";
17023 +                        %end;
17024 +                        %if "&EM_REPORT_KEY_XREF" ne "" %then %do; referenceline x=&EM_REPORT_KEY_XREF; %end;
17025 +                    endlayout;
17026 +                %end;
17028 +                %if &_misc_ ne 0 or ( &_vmisc_ ne 0 and &nonMiss_vmisc ne 0) %then %do;
17029 +                    layout overlay / cycleattrs=true
17030 +                        xaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, rpt_iteration_vlabel, NOQUOTE))")
17031 +                        yaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, rpt_misclassrate_vlabel, NOQUOTE))");
17032 +                        %if &_misc_ ne 0 %then %do;
17033 +                            seriesplot x=&X y=_MISC_ /  markerattrs=(size=0) display=all;
17034 +                        %end;
17035 +                        %if &_vmisc_  ne 0 %then %do ;
17036 +                            seriesplot x=&X y=_VMISC_ /  markerattrs=(size=0) display=all;
17037 +                        %end;
17038 +                        %if "&EM_REPORT_KEY_XREF" ne "" %then %do; referenceline x=&EM_REPORT_KEY_XREF; %end;
17039 +                    endlayout;
17040 +                %end;
17042 +                sidebar /  align=bottom;
17043 +                    layout overlay;
17044 +                        discretelegend 'train' 'validate' / down=1 order=columnmajor;
17045 +                    endlayout;
17046 +                endsidebar;
17047 +              endlayout;
17048 +            endgraph;
17049 +        end;
17050 +    run;
17052 +    proc sort data=&pdat.&EM_REPORT_KEY_WHERECLAUSE out=_tmprpt_iterdat;
17053 +        by &X;
17054 +    run;
17056 +    proc sgrender data=_tmprpt_iterdat template='em.IterationPlot';
17057 +    run;
17058 +    title1; title2; title3;
17060 +    proc delete data=_tmprpt_iterdat; run;
17062 +%mend  EM_REPORT_ITERATIONPLOT;
17064 +/* for hpdmassess */
17066 +%macro EM_REPORT_HPDMASSESS();
17068 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
17070 +    title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
17072 +    /*--- EM_REPORT_HPDMASSESS 1. idenfity the variables     ---*/
17074 +    %let dsid = %sysfunc(open(&pdat));
17075 +    %if &dsid %then %do;
17076 +        %let lift     = %sysfunc(VARNUM(&dsid, lift));
17077 +        %let c_lift   = %sysfunc(VARNUM(&dsid, c_lift));
17078 +        %let mean1    = %sysfunc(VARNUM(&dsid, target_mean));
17079 +        %let mean2    = %sysfunc(VARNUM(&dsid, predicted_mean));
17080 +        %let datarole = %sysfunc(VARNUM(&dsid, &EM_REPORT_KEY_GROUP));
17081 +    %end;
17082 +    %if &dsid %then %let dsid = %sysfunc(close(&dsid));
17084 +    %if "&lift"     eq "."  %then %let lift     = 0;
17085 +    %if "&c_lift"   eq "."  %then %let c_lift   = 0;
17086 +    %if "&mean1"    eq "."  %then %let mean1   = 0;
17087 +    %if "&mean2"    eq "."  %then %let mean2   = 0;
17088 +    %if "&datarole" eq "."  %then %let datarole = 0;
17090 +    %put &lift &c_lift &mean1 &mean2 &datarole;
17092 +    %if &lift ne 0 and &c_lift ne 0 %then %do;
17093 +        %let hpdm_plot = 1;
17094 +        %let hpdm_var1 = lift;
17095 +        %let hpdm_var2 = c_lift;
17096 +        %let hpdm_lbl1 = assmt_lift_vlabel;
17097 +        %let hpdm_lbl2 = assmt_liftc_vlabel;
17098 +    %end;
17099 +    %else
17100 +    %if &mean1 ne 0 and &mean2 ne 0
17101 +    %then %do;
17102 +        %let hpdm_plot = 1;
17103 +        %let hpdm_var1 = target_mean;
17104 +        %let hpdm_var2 = predicted_mean;
17105 +        %let hpdm_lbl1 = rpt_rpttargetmean_vlabel;
17106 +        %let hpdm_lbl2 = rpt_rptpredmean_vlabel;
17107 +    %end;
17108 +    %else %do;
17109 +        %let hpdm_plot = 0;
17110 +        %let hpdm_var1 = ;
17111 +        %let hpdm_var2 = ;
17112 +        %let hpdm_lbl1 = ;
17113 +        %let hpdm_lbl2 = ;
17114 +    %end;
17115 +    /*--- EM_REPORT_HPDMASSESS 2. call SGPANEL                ---*/
17117 +    %if &hpdm_plot eq 1 %then %do;
17118 +        ods path(prepend) work.templat(update);
17119 +        proc template;
17120 +            define statgraph em.hpdmassess / store=work.templat;
17121 +                begingraph;
17122 +                    entrytitle "&EM_REPORT_TTL1";
17123 +                    entrytitle "&EM_REPORT_TTL2";
17124 +                    entrytitle "&EM_REPORT_TTL3";
17126 +                    layout lattice / order=columnmajor rows=1 rowdatarange=data columngutter=10;
17128 +                        %if &datarole ne 0 %then %do;
17129 +                            layout overlay / cycleattrs=true
17130 +                                             xaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, rpt_depth_vlabel, NOQUOTE))")
17131 +                                             yaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, &hpdm_lbl1, NOQUOTE))")
17132 +                            ;
17133 +                                seriesplot x=&EM_REPORT_KEY_X y=&hpdm_var1 / name='var1' markerattrs=(size=0) display=all group=&EM_REPORT_KEY_GROUP;
17134 +                            endlayout;
17135 +                            layout overlay / cycleattrs=true
17136 +                                             xaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, rpt_depth_vlabel, NOQUOTE))")
17137 +                                             yaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, &hpdm_lbl2, NOQUOTE))")
17138 +                            ;
17139 +                                seriesplot x=&EM_REPORT_KEY_X y=&hpdm_var2 /  markerattrs=(size=0) display=all group=&EM_REPORT_KEY_GROUP;
17140 +                            endlayout;
17141 +                            sidebar /  align = bottom;
17142 +                                layout overlay;
17143 +                                    discretelegend 'var1' / down=1 order=columnmajor;
17144 +                                endlayout;
17145 +                            endsidebar;
17146 +                        %end;
17147 +                        %else %do;
17148 +                            layout overlay / cycleattrs=true
17149 +                                             xaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, rpt_depth_vlabel, NOQUOTE))")
17150 +                                             yaxisopts=(griddisplay=on label="%sysfunc(sasmsg(sashelp.dmine, &hpdm_lbl1, NOQUOTE))")
17151 +                            ;
17152 +                                seriesplot x=&EM_REPORT_KEY_X y=&hpdm_var1 /  markerattrs=(size=0) display=all name='var1'
17153 +                                    legendlabel="%sysfunc(sasmsg(sashelp.dmine, &hpdm_lbl1, NOQUOTE))";
17154 +                                seriesplot x=&EM_REPORT_KEY_X y=&hpdm_var2 /  markerattrs=(size=0) display=all name='var2'
17155 +                                    legendlabel="%sysfunc(sasmsg(sashelp.dmine, &hpdm_lbl2, NOQUOTE))";
17156 +                            endlayout;
17157 +                            sidebar /  align=bottom;
17158 +                                layout overlay;
17159 +                                    discretelegend 'var1' 'var2' / down=1 order=columnmajor;
17160 +                                endlayout;
17161 +                            endsidebar;
17162 +                        %end;
17163 +                    endlayout;
17164 +                endgraph;
17165 +            end;
17166 +        run;
17168 +        proc sgrender data=&pdat.&EM_REPORT_KEY_WHERECLAUSE template='em.hpdmassess'; run;
17169 +    %end;
17171 +    title1; title2; title3;
17173 +%mend EM_REPORT_HPDMASSESS;
17175 +/* for heatmap version of histograms */
17177 +%macro EM_REPORT_HEATMAPTEMPLATE();
17179 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
17181 +    %if "&EM_REPORT_KEY_FREQUENCY" ne "" "&EM_REPORT_KEY_X" ne "" "&EM_REPORT_KEY_Y" ne "" %then %do;
17183 +        title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
17185 +        proc template;
17186 +        define statgraph heatmap;
17187 +        begingraph;
17188 +        entrytitle "&EM_REPORT_TTL1";
17189 +        entrytitle "&EM_REPORT_TTL2";
17190 +        entrytitle "&EM_REPORT_TTL3";
17191 +        layout overlay;
17192 +        heatmapparm x=&EM_REPORT_KEY_X y=&EM_REPORT_KEY_Y colorresponse=&EM_REPORT_KEY_FREQUENCY / name="map" display=all;
17193 +          continuouslegend "map";
17194 +        endlayout;
17195 +        endgraph;
17196 +        end;
17198 +        proc sgrender data=&pdat.&EM_REPORT_KEY_WHERECLAUSE template=heatmap; run;
17200 +    %end;
17202 +    title1; title2; title3;
17204 +%mend EM_REPORT_HEATMAPTEMPLATE;
17207 +/* for assessment plot */
17209 +%macro EM_REPORT_EMRANK(pdat=, lattice=no) ;
17211 +    data _tmp_dat;
17212 +        set &pdat.&EM_REPORT_KEY_WHERECLAUSE;
17213 +    run;
17214 +    %let pdat = _tmp_dat;
17216 +    %let varList = GAIN CAPC LIFTC RESPC _MEANP_ _MINP_ _MAXP_;
17217 +    %let varNum = 7;
17218 +    %let modelVar = model;
17219 +    %do k = 1 %to &varNum;
17220 +        %let thisVar = %scan(&varList,&k,' ');
17221 +        %let var_&thisVar = ;
17222 +    %end;
17224 +/*-----------------------------------------------------------------------------*/
17226 +    proc contents data=&pdat out=_tmp_content(keep=name) noprint;
17227 +    run;
17229 +    %let is_cat = 0;
17230 +    %let has_loop = 0;
17231 +    %let has_where = 0;
17233 +    data _null_;
17234 +        set _tmp_content;
17235 +        name=strip(upcase(name));
17236 +        format _tmp_list $500.;
17237 +        _tmp_list = symget('varList');
17239 +        do k = 1 to &varNum;
17240 +            if name eq scan(_tmp_list,k,' ') then call symput(cats('var_',name),name);
17241 +        end;
17243 +        if name eq '_LOOP_'  then call symput('has_loop','1');
17244 +        if name eq '_WHERE_' then call symput('has_where','1');
17245 +        if name eq 'GAIN'    then call symput('is_cat','1');
17246 +    run;
17248 +    %if &has_loop eq 1 %then %do;
17249 +        %if &has_where eq 1 %then %do;
17250 +            proc sql noprint;
17251 +                select count(*) into :nn1 from &pdat;
17252 +                select count(*) into :nn2 from &pdat where _where_ is missing;
17253 +            quit;
17254 +            %if &nn1 ne &nn2
17255 +                %then %let modelvar = _where_;
17256 +                %else %let modelvar = ;
17257 +        %end;
17258 +        %else %do;
17259 +            %let modelvar = ;
17260 +        %end;
17261 +    %end;
17263 +/*-----------------------------------------------------------------------------*/
17265 +    %if "&lattice" eq "yes" %then %do ;
17267 +        proc sql noprint;
17268 +            select count(distinct datarole)  into :ndataroles from &pdat;
17269 +            select distinct datarole into :drole1-:drole%left(&ndataroles) from &pdat;
17270 +        quit;
17272 +        %do d =1 %to &ndataroles;
17274 +            proc sort data=&pdat(where=(datarole eq "&&&drole&d")) out=_tmp_emrp;
17275 +                by &modelvar decile;
17276 +            run;
17278 +            title1 "&EM_REPORT_TTL1";
17279 +            title2 "&EM_REPORT_TTL2";
17280 +            title3 "%sysfunc(sasmsg(sashelp.dmine, rpt_multiplemdlassess_title, NOQUOTE, &&&drole&d))";
17281 +            %if "&EM_REPORT_KEY_WHERE" ne " " %then %do;
17282 +                title4 "&EM_REPORT_KEY_WHERE";
17283 +            %end;
17285 +            proc sgscatter data=_tmp_emrp;
17286 +                plot
17287 +                    %if &is_cat eq 1 %then %do;
17288 +                        (&var_gain &var_liftc &var_capc &var__meanp_) * decile
17289 +                    %end;
17290 +                    %else %do;
17291 +                        (&var__meanp_ &var__minp_ &var__maxp_) * decile
17292 +                    %end;
17293 +                    / %if &modelvar ne %then %do; group=&modelvar %end;
17294 +                        join=() markerattrs=(size=0) grid
17295 +                ;
17296 +            run; quit;
17297 +            title1; title2; title3; title4;
17298 +        %end;
17299 +    %end;
17300 +    %else %do;
17302 +        proc sort data=&pdat out=_tmp_emrp;
17303 +            by target datarole;
17304 +        run;
17306 +        title1 "&EM_REPORT_TTL1";
17307 +        title2 "&EM_REPORT_TTL2";
17308 +        title3 "%sysfunc(sasmsg(sashelp.dmine, rpt_singlemdlassess_title, NOQUOTE, &em_report_key_where))";
17310 +        %if &is_cat eq 1 %then %do;
17311 +            %if "&var_gain" ne " " %then %do;
17312 +                proc sgpanel data=_tmp_emrp;
17313 +                    panelby datarole;
17314 +                    series x=decile y=gain; series x=decile y=basegain; series x=decile y=bestgain;
17315 +                    rowaxis grid; colaxis grid;
17316 +                run; quit;
17317 +            %end;
17318 +            %if "&var_liftc" ne " " %then %do;
17319 +                proc sgpanel data=_tmp_emrp;
17320 +                    panelby datarole;
17321 +                    series x=decile y=liftc; series x=decile y=baseliftc; series x=decile y=bestliftc;
17322 +                    rowaxis grid; colaxis grid;
17323 +                run; quit;
17324 +            %end;
17325 +            %if "&var_capc" ne " " %then %do;
17326 +                proc sgpanel data=_tmp_emrp;
17327 +                    panelby datarole;
17328 +                    series x=decile y=capc; series x=decile y=basecapc; series x=decile y=bestcapc;
17329 +                    rowaxis grid; colaxis grid;
17330 +                run; quit;
17331 +            %end;
17332 +        %end;
17333 +        %else %do;
17334 +            %if "&var__meanp_" ne " " %then %do ;
17335 +                proc sgpanel data=_tmp_emrp;
17336 +                    panelby datarole;
17337 +                    series x=decile y=_meanp_; series x=decile y=_targetmean_;
17338 +                    rowaxis grid; colaxis grid;
17339 +                run; quit;
17340 +            %end;
17341 +            %if "&var__minp_" ne " " %then %do ;
17342 +                proc sgpanel data=_tmp_emrp;
17343 +                    panelby datarole;
17344 +                    series x=decile y=_minp_; series x=decile y=_targetmin_;
17345 +                    rowaxis grid; colaxis grid;
17346 +                run; quit;
17347 +            %end;
17348 +            %if "&var__maxp_" ne " " %then %do ;
17349 +                proc sgpanel data=_tmp_emrp;
17350 +                    panelby datarole;
17351 +                    series x=decile y=_maxp_; series x=decile y=_targetmax_;
17352 +                    rowaxis grid; colaxis grid;
17353 +                run; quit;
17354 +            %end;
17355 +        %end;
17357 +        title1; title2; title3;
17358 +    %end;
17360 +%mend;
17363 +%macro EM_REPORT_LATTICE();
17365 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
17367 +    %let numx = 1;
17368 +    %let numy = 1;
17370 +    proc sql noprint;
17371 +        %if "&EM_REPORT_KEY_LATTICEX" ne "" %then %do;
17372 +            select count(distinct &EM_REPORT_KEY_LATTICEX) into :numx from &pdat.&EM_REPORT_KEY_WHERECLAUSE;
17373 +        %end;
17374 +        %if "&EM_REPORT_KEY_LATTICEY" ne "" %then %do;
17375 +            select count(distinct &EM_REPORT_KEY_LATTICEY) into :numy from &pdat.&EM_REPORT_KEY_WHERECLAUSE;
17376 +        %end;
17377 +    quit;
17379 +    %let numrows=&numy;
17380 +    %let numcols=&numx;
17381 +    %let totalnum = %eval(&numx * &numy);
17383 +    %if &totalnum gt 6 %then %do;
17384 +        %if &totalnum le 18 %then %let numcols=3;
17385 +        %else %if &totalnum le 24 %then %let numcols=4;
17386 +        %else %if &totalnum le 30 %then %let numcols=5;
17387 +        %else %let numcols=3;
17388 +    %end;
17390 +    %if &totalnum gt 6 %then %do;
17391 +        %let numrows  = %sysevalf(&totalnum / &numcols);
17392 +        %let numrows  = %sysfunc(ceil(&numrows));
17393 +        %if &numrows ge 6 %then %let numrows=6;
17394 +    %end;
17396 +    %if (("&EM_REPORT_KEY_LATTICETYPE" eq "BAR") OR ("&EM_REPORT_KEY_LATTICETYPE" eq "HISTOGRAM"))
17397 +    %then %do;
17399 +        title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
17400 +        %if "&em_report_key_whereclause" ne "" %then %do ;
17401 +            title4 "%sysfunc(sasmsg(sashelp.dmine, rpt_text_title, NOQUOTE, &em_report_key_where))";
17402 +        %end;
17404 +        proc sgpanel data=&pdat.&EM_REPORT_KEY_WHERECLAUSE;
17405 +            panelby
17406 +                %if "&EM_REPORT_KEY_LATTICEY" ne "" %then %do; &EM_REPORT_KEY_LATTICEY %end;
17407 +                %if "&EM_REPORT_KEY_LATTICEX" ne "" %then %do; &EM_REPORT_KEY_LATTICEX %end;
17408 +              /
17409 +                %if &totalnum ge 5 %then %do; columns=&numcols rows=&numrows /*novarname*/ %end;
17410 +                uniscale=column
17411 +            ;
17412 +            /* originally using frequency so keeping this here; */
17413 +            /* eventrate plot in binning sets y var as response so added y as well */
17415 +            %if "&EM_REPORT_KEY_LATTICETYPE" eq "HISTOGRAM" %then %do;
17416 +                %if "&EM_REPORT_KEY_FREQUENCY" eq "" %then %do;
17417 +                    histogram &EM_REPORT_KEY_X;
17418 +                %end;
17419 +                %else %do;
17420 +                    histogram &EM_REPORT_KEY_X / freq = &EM_REPORT_KEY_FREQUENCY;
17421 +                %end;
17422 +            %end;
17423 +            %else %do;
17424 +                %if "%upcase(&EM_REPORT_KEY_ORIENTATION)" eq "HORIZONTAL"
17425 +                %then %do; hbar %end;
17426 +                %else %do; vbar %end;
17427 +                    &EM_REPORT_KEY_X /
17428 +                    %if "&EM_REPORT_KEY_FREQUENCY" ne "" %then %do; response=&EM_REPORT_KEY_FREQUENCY %end;
17429 +                    %if "&EM_REPORT_KEY_Y" ne ""         %then %do; response=&EM_REPORT_KEY_Y         %end;
17430 +                    %if "&EM_REPORT_KEY_SUBGROUP" ne ""  %then %do; group=&EM_REPORT_KEY_SUBGROUP     %end;
17431 +                        %else %if "&EM_REPORT_KEY_GROUP" ne ""  %then %do; group=&EM_REPORT_KEY_GROUP %end;
17432 +                ;
17433 +            %end;
17434 +            colaxis grid fitpolicy=rotatethin /* discreteorder=data */;
17435 +            rowaxis grid ;
17436 +        run; quit;
17438 +        /* special treatments */
17439 +        %if "&EM_REPORT_KEY_DESCRIPTION" eq "EVENTRATE" %then %do ;
17440 +            %if (("&EM_REPORT_KEY_AUTODISPLAY" eq "Y") OR ("&EM_REPORT_SHOWALL" eq "Y")) %then %do;
17442 +                %let wtevexist=0;
17443 +                %let dsid = %sysfunc(open(work.pdl));
17444 +                %if &dsid %then %do;
17445 +                    %let wtevexist = %sysfunc(VARNUM(&dsid, wtev));
17446 +                %end;
17447 +                %if &dsid %then %let dsid = %sysfunc(close(&dsid));
17449 +                %EM_REPORT_TABLETEXT(subtitle=views_descriptions_&em_report_key_description);
17451 +                proc print data=&pdat.&EM_REPORT_KEY_WHERECLAUSE noobs label contents='';
17452 +                    var display_var _label_ _group_ role eventRate
17453 +                        %if &wtevexist ne 0 %then %do; wtev %end;;
17454 +                run;
17455 +            %end;
17456 +        %end;
17457 +    %end;
17458 +    %else %if (("&EM_REPORT_KEY_LATTICETYPE" eq "LINEPLOT") or ("&EM_REPORT_KEY_LATTICETYPE" eq "PLOT")) %then %do;
17460 +        proc sort data=&pdat.&EM_REPORT_KEY_WHERECLAUSE out=_tmp_pdl;
17461 +            by
17462 +                %if "&EM_REPORT_KEY_LATTICEY" ne "" %then %do; &EM_REPORT_KEY_LATTICEY %end;
17463 +                %if "&EM_REPORT_KEY_LATTICEX" ne "" %then %do; &EM_REPORT_KEY_LATTICEX %end;
17464 +            ;
17465 +        run;
17467 +        title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
17468 +        %if "&em_report_key_whereclause" ne "" %then %do ;
17469 +            title4 "%sysfunc(sasmsg(sashelp.dmine, rpt_text_title, NOQUOTE, &em_report_key_where))";
17470 +        %end;
17472 +        proc sgpanel data=_tmp_pdl;
17473 +            panelby
17474 +                %if "&EM_REPORT_KEY_LATTICEY" ne "" %then %do; &EM_REPORT_KEY_LATTICEY %end;
17475 +                %if "&EM_REPORT_KEY_LATTICEX" ne "" %then %do; &EM_REPORT_KEY_LATTICEX %end;
17476 +              /
17477 +                %if &totalnum ge 5 %then %do; columns=&numcols rows=&numrows /*novarname*/ %end;
17478 +                uniscale=column
17479 +            ;
17480 +            %let i = 1;
17481 +            %do %while ("%scan(&EM_REPORT_KEY_Y,&i,' ')" ne "");
17482 +                %let thisY=%scan(&EM_REPORT_KEY_Y,&i,' ');
17483 +                %let i = %eval(&i+1);
17484 +                series x=&EM_REPORT_KEY_X y=&thisY
17485 +                    %if "&EM_REPORT_KEY_GROUP" ne "" %then %do; / group=&EM_REPORT_KEY_GROUP %end;
17486 +                ;
17487 +            %end;
17488 +            %if "&EM_REPORT_KEY_Y2" ne "" %then %do;
17489 +                series x=&EM_REPORT_KEY_X y=&EM_REPORT_KEY_Y2
17490 +                    %if "&EM_REPORT_KEY_GROUP" ne "" %then %do; / group=&EM_REPORT_KEY_GROUP %end;
17491 +                ;
17492 +            %end;
17493 +            %if "&EM_REPORT_KEY_YREF" ne "" %then %do; refline &EM_REPORT_KEY_YREF / axis = y; %end;
17494 +            %if "&EM_REPORT_KEY_XREF" ne "" %then %do; refline &EM_REPORT_KEY_XREF / axis = x; %end;
17495 +            colaxis grid fitpolicy=rotatethin;
17496 +            rowaxis grid;
17497 +        run; quit;
17498 +    %end;
17500 +%mend EM_REPORT_LATTICE;
17502 +/*---------------------------------------------------------*/
17503 + * EM_REPORT_SCOREDIST2 ;
17504 + *     Score Distribution 2;
17505 +/*---------------------------------------------------------*/
17507 +%macro EM_REPORT_SCOREDIST2() ;
17509 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
17511 +    title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
17513 +    proc sgpanel data=&pdat.&EM_REPORT_KEY_WHERECLAUSE;
17514 +        panelby
17515 +            %if "&EM_REPORT_KEY_LATTICEY" ne "" %then %do; &EM_REPORT_KEY_LATTICEY %end;
17516 +            %if "&EM_REPORT_KEY_LATTICEX" ne "" %then %do; &EM_REPORT_KEY_LATTICEX %end;
17517 +          / rows=1 uniscale=row layout=lattice
17518 +        ;
17519 +        vbar &EM_REPORT_KEY_X
17520 +            %if "&EM_REPORT_KEY_FREQUENCY" ne "" %then %do; / response=count %end;
17521 +        ;
17522 +        colaxis fitpolicy=rotatethin;
17523 +    run; quit;
17525 +%mend EM_REPORT_SCOREDIST2;
17527 +/*---------------------------------------------------------*/
17528 + * EM_REPORT_SCOREDIST ;
17529 + *     Score Distribution ;
17530 +/*---------------------------------------------------------*/
17532 +%macro EM_REPORT_SG4SCOREDIST(y1=,y2=,str1=,str2=,rowstr=);
17533 +    proc sgpanel data=_tmpdat;
17534 +        panelby datarole;
17535 +        %if "&y1" ne " " %then %do;
17536 +            series x=score y=&y1 / legendlabel="%sysfunc(sasmsg(sashelp.dmine, &str1, NOQUOTE))";
17537 +        %end;
17538 +        %if "&y2" ne " " %then %do;
17539 +            series x=score y=&y2 / legendlabel="%sysfunc(sasmsg(sashelp.dmine, &str2, NOQUOTE))";
17540 +        %end;
17541 +        rowaxis grid label="%sysfunc(sasmsg(sashelp.dmine, &rowstr, NOQUOTE))";
17542 +        colaxis grid;
17543 +    run; quit;
17544 +%mend;
17546 +%macro EM_REPORT_SCOREDIST(data=) ;
17548 +    data _tmp_scoredist;
17549 +        set &EM_REPORT_LIB..&EM_REPORT_KEY_FILE.&EM_REPORT_KEY_WHERECLAUSE;
17550 +    run;
17552 +    proc contents data=_tmp_scoredist out=_tmp_content(keep=varnum name) noprint;
17553 +    run;
17555 +    /*--------------------------------------------------------------------------*/
17557 +    %let var_targetMean = ;
17558 +    %let var_targetMax = ;
17559 +    %let var_targetMin = ;
17560 +    %let is_cat = 0;
17561 +    %let modeldescnum = 0;
17562 +    %let loopnum = 0;
17563 +    %let has_where = 0;
17564 +    %let has_target = 0;
17566 +    data _null_;
17567 +        set _tmp_content;
17568 +        name=strip(upcase(name));
17569 +        if name eq 'MODELDESCRIPTION'   then call symput('modeldescnum',  put(varnum,6.));
17570 +        if name eq '_LOOP_'             then call symput('loopnum',       put(varnum,6.));
17571 +        if name eq '_WHERE_'            then call symput('has_where',     '1');
17572 +        if name eq '_TARGET_'           then call symput('has_target',    '1');
17573 +        if name eq '_TARGETMEAN_'       then call symput('var_targetMean',name);
17574 +        if name eq '_TARGETMAX_'        then call symput('var_targetMax', name);
17575 +        if name eq '_TARGETMIN_'        then call symput('var_targetMin', name);
17576 +        if name eq 'PERCENTAGEOFEVENTS' then call symput('is_cat',        '1');
17577 +    run;
17579 +    /*--------------------------------------------------------------------------*/
17581 +    %if (&modeldescnum eq 0) and (&loopnum eq 0) %then %do;
17582 +        %let modelvar = ;
17583 +        %let nmodels = 1;
17584 +        %let modeldat1 = _tmp_scoredist;
17585 +    %end;
17586 +    %else %do;
17588 +        %if &loopnum gt 0
17589 +            %then %do;
17590 +                proc sql noprint;
17591 +                    select count(*) into :nn1 from _tmp_scoredist;
17592 +                    select count(*) into :nn2 from _tmp_scoredist where _where_ is missing;
17593 +                quit;
17594 +                %if &nn1 ne &nn2
17595 +                    %then %let modelvar = _where_;
17596 +                    %else %let modelvar = target;
17597 +            %end;
17598 +            %else %let modelvar = model;
17600 +        %let nmodels = 1;
17601 +        proc sql noprint;
17602 +            select count(distinct &modelvar) into :nmodels from _tmp_scoredist;
17603 +            select distinct &modelvar into :cond1-:cond%left(&nmodels) from _tmp_scoredist;
17604 +        quit;
17606 +        %do d = 1 %to &nmodels;
17607 +            %let modeldat&d = %str(_tmp_scoredist(where=(&modelvar eq "&&&cond&d")));
17608 +        %end;
17609 +    %end;
17611 +    /* --- loop through all models or _where_ for plots---*/
17613 +    %do d = 1 %to &nmodels;
17615 +        proc sort data=&&&modeldat&d out=_tmpdat;
17616 +            by &modelvar target datarole;
17617 +        run;
17619 +        title1 "&EM_REPORT_TTL1";
17620 +        title2 "&EM_REPORT_TTL2";
17621 +        title3 "%sysfunc(sasmsg(sashelp.dmine, rpt_scoredistsingle_title, NOQUOTE, &em_report_key_where))";
17622 +        %if &nmodels gt 1 %then %do; title4 "&&&cond&d"; %end;
17624 +        %if &is_cat eq 1 %then %do;
17625 +            %EM_REPORT_SG4SCOREDIST(y1=percentageofevents, y2=percentageofnonevents,
17626 +                str1=rpt_events_vlabel, str2=rpt_nonevents_vlabel, rowstr=assmt_percent_vlabel);
17627 +            %EM_REPORT_SG4SCOREDIST(y1=cumulativepercentageofevents, y2=cumulativepercentageofnonevents,
17628 +                str1=rpt_events_vlabel, str2=rpt_nonevents_vlabel, rowstr=assmt_percentC_vlabel);
17629 +        %end;
17630 +        %else %do;
17631 +            %EM_REPORT_SG4SCOREDIST(y1=_meanP_, y2=&var_targetMean,
17632 +                str1=rpt_predicted_vlabel, str2=rpt_target_vlabel, rowstr=rpt_mean_vlabel);
17633 +            %EM_REPORT_SG4SCOREDIST(y1=_meanP_, y2=&var_targetMax,
17634 +                str1=rpt_predicted_vlabel, str2=rpt_target_vlabel, rowstr=rpt_maximum_vlabel);
17635 +        %end;
17637 +        proc delete data=_tmpdat; run;
17638 +        title1; title2; title3; title4;
17639 +    %end;
17641 +    /* --- loop through all models or _where_ for prints ---*/
17643 +    %do d = 1 %to &nmodels;
17645 +        proc sort data=&&&modeldat&d out=_tmpdat;
17646 +            by &modelvar target datarole;
17647 +        run;
17649 +        title1; title2; title3;
17650 +        %EM_REPORT_TABLETEXT(subtitle=rpt_scoredistnone_title);
17652 +        %if &is_cat eq 1 %then %do;
17653 +            proc print data=_tmpdat noobs label contents='';
17654 +                by &modelvar target datarole;
17655 +                var p_label numberofevents percentageofevents percentageofnonevents
17656 +                    cumulativepercentageofevents cumulativepercentageofnonevents ;
17657 +            run;
17658 +        %end;
17659 +        %else %do;
17660 +            proc print data=_tmpdat noobs label contents='';
17661 +                by &modelvar target datarole;
17662 +                var p_label _meanP_ _maxP_ _minP_ &var_targetMean &var_targetMax &var_targetMin;
17663 +            run;
17664 +        %end;
17665 +    %end;
17667 +%mend EM_REPORT_SCOREDIST;
17670 +%macro EM_REPORT_MULTIPLY2PLOT;
17672 +    data _tmp_dat;
17673 +        set &EM_REPORT_LIB..&EM_REPORT_KEY_FILE.&EM_REPORT_KEY_WHERECLAUSE;
17674 +    run;
17676 +    proc contents data=_tmp_dat out=_tmp_content(keep=name) noprint;
17677 +    run;
17679 +    %let has_datarole = 0;
17680 +    data _null_;
17681 +        set _tmp_content;
17682 +        name=strip(upcase(name));
17683 +        if name eq "DATEROLE" then call symput('has_datarole','1');
17684 +    run;
17686 +    %if &has_datarole eq 1 %then %do;
17687 +        proc sql noprint;
17688 +            select count(distinct datarole) into :ndataroles from _tmp_dat;
17689 +            select distinct datarole into :drole1-:drole%left(&ndataroles) from _tmp_dat;
17690 +        quit;
17691 +    %end;
17692 +    %else %do;
17693 +        %let ndataroles = 1;
17694 +    %end;
17696 +    %if &ndataroles gt 1 %then %do;
17697 +        %do d=1 %to &ndataroles ;
17699 +            proc sort data=_tmp_dat(where=(datarole eq "&&&drole&d")) out=_tmp_empd;
17700 +                by datarole &EM_REPORT_KEY_X;
17701 +            run;
17703 +            title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
17704 +            title4 "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitlethree_title, NOQUOTE, &&&drole&d))";
17706 +            proc sgplot data=_tmp_empd;
17707 +                series x=&EM_REPORT_KEY_X  y=&EM_REPORT_KEY_Y   ;
17708 +                series x=&EM_REPORT_KEY_X  y=&EM_REPORT_KEY_Y2 / y2axis ;
17709 +                %if "&EM_REPORT_KEY_YREF"  ne "" %then %do; refline &EM_REPORT_KEY_YREF  / axis = y;  %end;
17710 +                %if "&EM_REPORT_KEY_Y2REF" ne "" %then %do; refline &EM_REPORT_KEY_Y2REF / axis = y2; %end;
17711 +            run; quit;
17712 +        %end;
17713 +    %end;
17714 +    %else %do;
17715 +        title1 "&EM_REPORT_TTL1"; title2 "&EM_REPORT_TTL2"; title3 "&EM_REPORT_TTL3";
17716 +        proc sgplot data=_tmp_dat;
17717 +            series x=&EM_REPORT_KEY_X  y=&EM_REPORT_KEY_Y   ;
17718 +            series x=&EM_REPORT_KEY_X  y=&EM_REPORT_KEY_Y2 / y2axis ;
17719 +            %if "&EM_REPORT_KEY_YREF"  ne "" %then %do; refline &EM_REPORT_KEY_YREF  / axis = y;  %end;
17720 +            %if "&EM_REPORT_KEY_Y2REF" ne "" %then %do; refline &EM_REPORT_KEY_Y2REF / axis = y2; %end;
17721 +        run; quit;
17722 +    %end;
17724 +    title1; title2; title3; title4;
17726 +%mend EM_REPORT_MULTIPLY2PLOT;
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMUTIL.REPORTER_PRINTMACROS.SOURCE.
17727 +/*---------------------------------------------------------*/
17728 + * EM_REPORT_NODE_TEXT ;
17729 + *      return the text label for a given node ;
17730 +/*---------------------------------------------------------*/
17732 +%macro EM_REPORT_NODE_TEXT(nodeid);
17733 +    %global EM_REPORT_NODE_TEXT ;
17734 +    %if not %sysfunc(exist(&em_report_lib..EM_NODEID)) %then %goto skip ;
17735 +    data _null_ ;
17736 +        set &em_report_lib..EM_NODEID ;
17737 +        where upcase(strip(nodeid)) eq upcase(strip("&nodeid")) ;
17738 +        call symput("EM_REPORT_NODE_TEXT",strip(tranwrd(label,'"','')));
17739 +    run;
17740 +    %skip:
17741 +%mend EM_REPORT_NODE_TEXT;
17743 +/*---------------------------------------------------------*/
17744 + * EM_REPORT_TABLETEXT;
17745 + *      print the ods texts in front of a table;
17746 +/*---------------------------------------------------------*/
17748 +%macro EM_REPORT_TABLETEXT(subtitle=);
17749 +%if %substr(%upcase(%sysfunc(strip(&subtitle))), 1, 19) eq VIEWS_DESCRIPTIONS_ %then %do;
17750 +    ods &EM_REPORT_FORMAT text=" ";
17751 +    ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text)))";
17752 +    %let thisDVD = dmine;
17754 +    %let msg0 = views_descriptions_&em_report_key_description;
17755 +    %let msg1 = %sysfunc(sasmsg(sashelp.dmine, views_descriptions_&em_report_key_description, NOQUOTE));
17756 +    %if %sysfunc(exist(sashelp.tmine)) %then %do;
17757 +        %let msg2 = %sysfunc(sasmsg(sashelp.tmine, views_descriptions_&em_report_key_description, NOQUOTE));
17758 +    %end;
17759 +    %else %do;
17760 +        %let msg2=views_descriptions_&em_report_key_description;
17761 +    %end;
17763 +    %if "%sysfunc(strip(%nrstr(&msg1)))" ne "%sysfunc(strip(&msg0))"
17764 +    %then %do;
17765 +        ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, views_descriptions_&em_report_key_description, NOQUOTE))";
17766 +    %end;
17767 +    %else %if "%sysfunc(strip(%nrstr(&msg2)))" ne "%sysfunc(strip(&msg0))"
17768 +    %then %do;
17769 +        %if %sysfunc(exist(sashelp.tmine)) %then %do;
17770 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.tmine, views_descriptions_&em_report_key_description, NOQUOTE))";
17771 +            %let thisDVD = tmine;
17772 +        %end;
17773 +    %end;
17774 +    %else %do;
17775 +        ods &EM_REPORT_FORMAT text="&em_report_key_description";
17776 +    %end;
17778 +    ods proclabel = "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text))) %sysfunc(sasmsg(sashelp.&thisDVD, &subtitle, NOQUOTE))";
17779 +%end;
17780 +%else %do;
17781 +    %let msg1 = %sysfunc(sasmsg(sashelp.dmine, &subtitle, NOQUOTE));
17782 +    %if %sysfunc(exist(sashelp.tmine)) %then %do;
17783 +        %let msg2 = %sysfunc(sasmsg(sashelp.tmine, &subtitle, NOQUOTE));
17784 +    %end;
17785 +    %else %do;
17786 +        %let msg2=%sysfunc(sasmsg(sashelp.dmine, &subtitle, NOQUOTE));
17787 +    %end;
17789 +    %if "%sysfunc(strip(%nrstr(&msg1)))" ne "%sysfunc(strip(&subtitle))"
17790 +    %then %do;
17791 +        %let thisDVD = dmine;
17792 +    %end;
17793 +    %else %if "%sysfunc(strip(%nrstr(&msg2)))" ne "%sysfunc(strip(&subtitle))"
17794 +    %then %do;
17795 +        %let thisDVD = tmine;
17796 +    %end;
17797 +    %else %do;
17798 +        %let thisDVD = dmine;
17799 +    %end;
17801 +    ods &EM_REPORT_FORMAT text=" ";
17802 +    ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text)))";
17803 +    ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.&thisDVD, &subtitle, NOQUOTE))";
17804 +    ods proclabel = "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text))) %sysfunc(sasmsg(sashelp.&thisDVD, &subtitle, NOQUOTE))";
17805 +%end;
17806 +%mend EM_REPORT_TABLETEXT;
17808 +/*---------------------------------------------------------*/
17809 + *  Print comparison of model fit stats;
17810 + *      used by EM_REPORT_DATA();
17811 +/*---------------------------------------------------------*/
17813 +%macro EM_REPORT_COMPAREFIT(data=) ;
17814 +    proc sort data=&data out=sd(drop=stat) ;
17815 +        by target datarole ;
17816 +    run ;
17817 +    %EM_REPORT_TABLETEXT(subtitle=rpt_modelfitcomparison_title);
17818 +    proc print data=sd noobs label contents='' ;
17819 +        by target datarole ;
17820 +        label label="%sysfunc(sasmsg(sashelp.dmine, rpt_dmneural_label_stat, NOQUOTE))";
17821 +    run;
17822 +%mend EM_REPORT_COMPAREFIT;
17824 +/*---------------------------------------------------------*/
17825 + * EM_REPORT_CLASSIFYTABT;
17826 + *      print the table in a lattice plot of classification charts;
17827 +/*---------------------------------------------------------*/
17829 +%macro EM_REPORT_CLASSIFYTAB();
17831 +    %if "&em_report_key_whereclause" eq "" %then %do ;
17832 +        %EM_REPORT_TABLETEXT(subtitle=rpt_eventclassification_title);
17833 +    %end; %else %do;
17834 +        ods &EM_REPORT_FORMAT text=" ";
17835 +        ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text)))";
17836 +        ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_eventclasswhere_title, NOQUOTE, &em_report_key_where))";
17837 +        ods proclabel = "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitletwo_title, NOQUOTE, %nrbquote(&em_report_node_text))) %sysfunc(sasmsg(sashelp.dmine, rpt_eventclasswhere_title, NOQUOTE, &em_report_key_where))";
17838 +    %end;
17840 +    %let pdat = &EM_REPORT_LIB..&EM_REPORT_KEY_FILE;
17842 +    proc print data=&pdat.&EM_REPORT_KEY_WHERECLAUSE noobs label contents='';
17843 +        label CORRECTTEXT= "%sysfunc(sasmsg(sashelp.dmine, rpt_state_vlabel, NOQUOTE))" percent="%sysfunc(sasmsg(sashelp.dmine, rpt_percent_vlabel, NOQUOTE))" ;
17844 +        by target datarole ;
17845 +        var from into correcttext count percent ;
17846 +    run;
17848 +%mend EM_REPORT_CLASSIFYTAB;
17850 +/*---------------------------------------------------------*/
17851 + * EM_REPORT_PROPERTIES;
17852 + *     print the properties table;
17853 +/*---------------------------------------------------------*/
17855 +%macro EM_REPORT_PROPERTIES(catalog=, dsname=work.properties);
17856 +    %if %sysfunc(cexist(&catalog)) %then %do;
17857 +        proc display cat=sashelp.emutil.prop2Ds.scl;
17858 +        run;
17859 +        %EM_REPORT_TABLETEXT(subtitle=rpt_props_title);
17860 +        %EM_REPORT_makeMultiColumn(dat=work.properties, vars=name value default, split=3);
17861 +        proc delete data=work.properties; run; quit;
17862 +    %end;
17863 +%mend EM_REPORT_PROPERTIES;
17865 +/*---------------------------------------------------------*/
17866 + * EM_REPORT_DATASOURCE ;
17867 + *     Report on data set starting a flow ;
17868 +/*--------------------------------------------------------*/
17869 +%macro EM_REPORT_DATASOURCE();
17871 +    proc transpose data=&EM_REPORT_LIB..&EM_REPORT_NODEID._EMTMETADEFAULT out=temp_source;
17872 +        var _all_;
17873 +        format crdate NLDATM24.0 modate NLDATM24.0;
17874 +    run;
17876 +    /* prepare the data source table */
17877 +    data temp_source;
17878 +        set temp_source;
17879 +        rename col1 = value;
17880 +        col1 = right(col1);
17881 +        format label $64.;
17882 +        label
17883 +            col1  = "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel, NOQUOTE))"
17884 +            label = "%sysfunc(sasmsg(sashelp.dmine, rpt_attribute_vlabel, NOQUOTE))"
17885 +        ;
17886 +        *** get the localized label column;
17887 +        _name_=upcase(_name_) ;
17888 +        if _name_ eq "MEMNAME"         then label="%sysfunc(sasmsg(sashelp.dmine, rpt_dataname_vlabel, NOQUOTE))" ;
17889 +        if _name_ eq "MEMTYPE"         then label="%sysfunc(sasmsg(sashelp.dmine, rpt_datatype_vlabel, NOQUOTE))" ;
17890 +        if _name_ eq "MEMLABEL"        then label="%sysfunc(sasmsg(sashelp.dmine, rpt_datalabel_vlabel, NOQUOTE))" ;
17891 +        if _name_ eq "TYPEMEM"         then delete ;
17892 +        if _name_ eq "ENGINE"          then label="%sysfunc(sasmsg(sashelp.dmine, rpt_engine_vlabel, NOQUOTE))" ;
17893 +        if _name_ eq "CRDATE"          then label="%sysfunc(sasmsg(sashelp.dmine, rpt_crdate_vlabel, NOQUOTE))" ;
17894 +        if _name_ eq "MODATE"          then label="%sysfunc(sasmsg(sashelp.dmine, rpt_datemode_vlabel, NOQUOTE))" ;
17895 +        if _name_ eq "NOBS"            then label="%sysfunc(sasmsg(sashelp.dmine, rpt_numrows_vlabel, NOQUOTE))" ;
17896 +        if _name_ eq "NCOLS"           then label="%sysfunc(sasmsg(sashelp.dmine, rpt_numcols_vlabel, NOQUOTE))" ;
17897 +        if _name_ eq "NBYTES"          then label="%sysfunc(sasmsg(sashelp.dmine, rpt_datasize_vlabel, NOQUOTE))" ;
17898 +        if _name_ eq "ROLE"            then label="%sysfunc(sasmsg(sashelp.dmine, meta_role_vlabel, NOQUOTE))" ;
17899 +        if _name_ eq "USEEXTERNALDATA" then delete ;
17900 +        if _name_ eq "SAMPLINGRATE"    then delete ;
17901 +        if _name_ eq "SEGMENT"         then label="%sysfunc(sasmsg(sashelp.dmine, rpt_segment_vlabel, NOQUOTE))" ;
17902 +        if _name_ eq "LIBNAME"         then label="%sysfunc(sasmsg(sashelp.dmine, rpt_datalib_vlabel, NOQUOTE))" ;
17903 +    run;
17905 +    %EM_REPORT_TABLETEXT(subtitle=rpt_dataattributes_title);
17906 +    %EM_REPORT_makeMultiColumn(dat=temp_source, vars=label value, split=3);
17907 +    proc delete data=temp_source; run; quit;
17909 +%mend EM_REPORT_DATASOURCE ;
17911 +*---------------------------------------------------------*/
17912 + *  a utility macro for split table into multiple columns ;
17913 +/*---------------------------------------------------------*/
17915 +%macro em_report_makeMultiColumn(dat=,vars=,split=2);
17917 +    %let vars = %upcase(&vars);
17919 +    /*--- check if the variable existing in the table ---*/
17920 +    proc contents data=&dat out=_tmpms_content(keep=name) noprint;
17921 +    run;
17923 +    %let k = 1;
17924 +    %let vars2 = ;
17925 +    %do %while("%scan(&vars,&k,' ')" ne "");
17926 +        data _null_;
17927 +            set _tmpms_content;
17928 +            name=strip(upcase(name));
17929 +            if name eq "%scan(&vars,&k,' ')"
17930 +                then call symput('vars2',catx(' ',symget('vars2'),name));
17931 +        run;
17932 +        %let k = %eval(&k+1);
17933 +    %end;
17935 +    /*--- count the valid variables ---*/
17936 +    %let k = 1;
17937 +    %do %while("%scan(&vars2,&k,' ')" ne "");
17938 +        %let var&k = %scan(&vars2,&k,' ');
17939 +        %let k = %eval(&k+1);
17940 +    %end;
17941 +    %let numvars = %eval(&k-1);
17943 +    /* --- split the table ---*/
17944 +    %let dsid = %sysfunc(open(&dat));
17945 +    %let numobs = %sysfunc(attrn(&dsid, NOBS));
17946 +    %let numrows = %sysfunc(round(%sysevalf(&numobs/&split)));
17947 +    %if &dsid %then %let dsid = %sysfunc(close(&dsid));
17949 +    data %do d = 1 %to &split; _tmpms_dat&d.(rename=(%do k = 1 %to &numvars; &&&var&k=&&&var&k..&d %end;)) %end;;
17950 +        set &dat;
17951 +        keep &vars2 numrow;
17952 +        %do d = 1 %to &split;
17953 +            if _n_ gt %eval(&numrows*(&d-1)) and _n_ le %eval(&numrows*&d)
17954 +                then do; numrow = _n_-&numrows*(&d-1); output _tmpms_dat&d; end;
17955 +        %end;
17956 +    run;
17958 +    %do d = 1 %to &split;
17959 +        proc sort data=_tmpms_dat&d; by numrow; run;
17960 +    %end;
17962 +    /*--- merge and print ---*/
17963 +    data _tmpms_alldat;
17964 +        merge %do d = 1 %to &split; _tmpms_dat&d %end;;
17965 +        by numrow;
17966 +    run;
17968 +    proc print data=_tmpms_alldat noobs label contents='';
17969 +        var %do d = 1 %to &split; %do k = 1 %to &numvars; &&&var&k..&d %end; %end;;
17970 +    run;
17972 +    proc datasets lib=work nolist;
17973 +        delete _tmpms_:;
17974 +    run; quit;
17976 +%mend em_report_makeMultiColumn;
17978 +*---------------------------------------------------------*/
17979 + *  basic print a data table ;
17980 +/*---------------------------------------------------------*/
17981 +%macro EM_REPORT_DATA(maxobs=100);
17983 +%local pd desc ;
17984 +%let pd=&em_report_lib..&EM_REPORT_KEY_FILE ;
17985 +%IF not %SYSFUNC(EXIST(&pd)) %then %goto skip;
17987 +%if (("&EM_REPORT_KEY_AUTODISPLAY" eq "Y") OR ("&EM_REPORT_SHOWALL" eq "Y")) %then %do;
17989 +    %let desc= %upcase(&em_report_key_description) ;
17991 +    %if "&desc" eq "VARIABLES" %then %do;
17992 +        /*--- NOOP: handled globally by EM_REPORT_NODE --*/
17993 +        %goto skip ;
17994 +    %end;
17995 +    %else %if (("&desc" eq "ITEMS") OR ("&desc" eq "OUTAOV16") OR ("&desc" eq "OUTGINT") OR ("&desc" eq "OUTGVAR")
17996 +              OR ("&desc" eq "HISTWEIGHT") OR ("&desc" eq "EMSTAT")) %then %do;;
17997 +        %goto skip;
17998 +    %end;
17999 +    %if (("&desc" eq "SAMPLE") AND (%substr(%upcase(%sysfunc(strip(&nodeid))), 1, 7) eq GRFEXPL)) %then %do;;
18000 +        %goto skip;
18001 +    %end;
18002 +    %else %if "&desc" eq "MODELDIAGNOSTICS" %then %do;
18004 +        %EM_REPORT_TABLETEXT(subtitle=views_descriptions_&em_report_key_description);
18006 +        proc sort data=&pd(obs=&maxobs) out=_pd; by datarole; run;
18008 +        proc print data=_pd noobs label contents='';
18009 +            by datarole;
18010 +            var cutoff sensitivity_tpr specificity_tnr one_specificity_fpr precision_event
18011 +                precision_non_event ovl_class_rate precision_overall;
18012 +        run;
18014 +        proc delete data=_pd; run; quit;
18015 +    %end;
18016 +    %else
18017 +    %if "&desc" eq "REPORTFITSTAT" %then %do ;
18019 +        /*--- should switch to proc tabulate ---*/
18020 +        /*--- often there is only one value of the by variable 'target', but there may be several ---*/
18021 +        data pd; set &pd ;
18022 +            where train ne . ;
18023 +            label _NOBS_  = "%sysfunc(sasmsg(sashelp.dmine, stat_nobs_vlabel, NOQUOTE))"
18024 +                  _MISC_  = "%sysfunc(sasmsg(sashelp.dmine, stat_misc_vlabel, NOQUOTE))"
18025 +                  _MAX_   = "%sysfunc(sasmsg(sashelp.dmine, stat_max_vlabel, NOQUOTE))"
18026 +                  _SSE_   = "%sysfunc(sasmsg(sashelp.dmine, stat_sse_vlabel, NOQUOTE))"
18027 +                  _ASE_   = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_ase, NOQUOTE))"
18028 +                  _RASE_  = "%sysfunc(sasmsg(sashelp.dmine, stat_rase_vlabel, NOQUOTE))"
18029 +                  _DIV_   = "%sysfunc(sasmsg(sashelp.dmine, stat_div_vlabel, NOQUOTE))"
18030 +                  _DFT_   = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_dft, NOQUOTE))"
18031 +                  _AIC_   = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_aic, NOQUOTE))"
18032 +                  _MSE_   = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_mse, NOQUOTE))"
18033 +                  _RMSE_  = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_rmse, NOQUOTE))"
18034 +                  _AVERR_ = "%sysfunc(sasmsg(sashelp.dmine, stat_averr_vlabel, NOQUOTE))"
18035 +                  _DFE_   = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_dfe, NOQUOTE))"
18036 +                  _DFM_   = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_dfm, NOQUOTE))"
18037 +                  _ERR_   = "%sysfunc(sasmsg(sashelp.dmine, stat_err_vlabel, NOQUOTE))"
18038 +                  _FPE_   = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_fpe, NOQUOTE))"
18039 +                  _NW_    = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_nw, NOQUOTE))"
18040 +                  _RFPE_  = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_rfpe, NOQUOTE))"
18041 +                  _SBC_   = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_sbc, NOQUOTE))"
18042 +            ;
18043 +            drop stat ;
18044 +        run;
18046 +        %EM_REPORT_TABLETEXT(subtitle=rpt_modelfitstat_title);
18048 +        proc sort data=pd;
18049 +            by target targetlabel;
18050 +        proc print data=pd noobs label contents='';
18051 +            label label="%sysfunc(sasmsg(sashelp.dmine, rpt_dmneural_label_statlabel, NOQUOTE))" ;
18052 +            by target targetlabel;
18053 +        run;
18055 +    %end ;
18056 +    %else %if "&desc" eq "CLUSMEAN" %then %do;
18058 +        /* determine number of segments to process */
18059 +        %let dsid = %sysfunc(open(&pd));
18060 +        %if &dsid %then %do;
18061 +            %let numseg = %sysfunc(ATTRN(&dsid, NOBS));
18062 +        %end;
18063 +        %if &dsid %then %let dsid = %sysfunc(close(&dsid));
18065 +        /* transpose data to have a row per segment value */
18066 +        proc transpose data=&pd out=clusmean name=VARIABLE;
18067 +        run;
18069 +        data clusmean;
18070 +            set clusmean;
18071 +            label _LABEL_ = "%sysfunc(sasmsg(sashelp.dmine, rpt_variable_vlabel, NOQUOTE))"
18072 +                %do i=1 %to &numseg;
18073 +                    %let colname = COL&i;
18074 +                    &colname = "%sysfunc(sasmsg(sashelp.dmine, rpt_segcolname_vlabel, NOQUOTE, &i))"
18075 +                %end;
18076 +            ;
18077 +            if missing(_LABEL_) then _LABEL_ = VARIABLE;
18078 +            if VARIABLE="_SEGMENT_" then delete;
18079 +            drop VARIABLE;
18080 +       run;
18082 +       %EM_REPORT_TABLETEXT(subtitle=rpt_meanstats_title);
18083 +       proc print data=clusmean noobs label contents='' ;  run ;
18085 +    %end;
18086 +    %else %if "&desc" eq "COMPAREFIT" %then %do;
18088 +        %EM_REPORT_COMPAREFIT(data=&pd);
18090 +    %end;
18091 +    %else %if "&desc" eq "FITSTAT" %then %do;
18093 +        /*** only print the fitstat table for MDLCOMP nodes ***/
18094 +        %if %substr(%upcase(%sysfunc(strip(&nodeid))), 1, 7) eq MDLCOMP %then %do;
18096 +            data _null_ ;
18097 +                set &pd (obs=2) end=eof ;
18098 +                if eof then do ;
18099 +                    call symput("_misc_"      , strip(put(_misc_,best.))) ;
18100 +                    call symput("_vmisc_"     , strip(put(_vmisc_,best.))) ;
18101 +                    call symput("_aur_"       , strip(put(_aur_, best.)));
18102 +                    call symput("_gini_"      , strip(put(_gini_, best.)));
18103 +                end;
18104 +            run;
18106 +            %let loopflag  = 0; %let validflag = 0;
18107 +            %let ASEflag   = 0; %let VASEflag  = 0; %let MISCflag  = 0; %let VMISCflag = 0;
18108 +            %let KSflag    = 0; %let VKSflag   = 0; %let AURflag   = 0; %let GINIflag  = 0;
18109 +            %let dsid = %sysfunc(open(&pd));
18110 +            %if &dsid %then %do;
18111 +                %let loopflag  = %sysfunc(VARNUM(&dsid, _LOOP_));
18112 +                %let validflag = %sysfunc(VARNUM(&dsid, VKS));
18113 +                %let ASEflag   = %sysfunc(VARNUM(&dsid, _ASE_));
18114 +                %let MISCflag  = %sysfunc(VARNUM(&dsid, _MISC_));
18115 +                %let KSflag    = %sysfunc(VARNUM(&dsid, KS));
18116 +                %let VASEflag  = %sysfunc(VARNUM(&dsid, _VASE_));
18117 +                %let VMISCflag = %sysfunc(VARNUM(&dsid, _VMISC_));
18118 +                %let VKSflag   = %sysfunc(VARNUM(&dsid, VKS));
18119 +                %let AURflag   = %sysfunc(VARNUM(&dsid, _AUR_));
18120 +                %let GINIflag  = %sysfunc(VARNUM(&dsid, _GINI_));
18121 +            %end;
18122 +            %if &dsid %then %let dsid=%sysfunc(close(&dsid));
18124 +            %EM_REPORT_TABLETEXT(subtitle=rpt_fittable_title);
18126 +            proc print data=&pd(obs=&maxobs) noobs label contents='';
18127 +                id
18128 +                    %if &loopflag ne 0 %then %do; _LOOP_ _WHERE_ %end;
18129 +                    USE PARENT MODEL MODELDESCRIPTION TARGET TARGETLABEL _CRITERION_
18130 +                ;
18131 +                var
18132 +                %if &validflag ne 0 %then %do;
18133 +                    %if &ASEflag ne 0                         %then %do; _ASE_   %end;
18134 +                    %if &MISCflag ne 0  and "&_misc_" ne "."  %then %do; _MISC_  %end;
18135 +                    %if &KSflag ne 0                          %then %do; KS      %end;
18136 +                    %if &VASEflag ne 0                        %then %do; _VASE_  %end;
18137 +                    %if &VMISCflag ne 0 and "&_vmisc_" ne "." %then %do; _VMISC_ %end;
18138 +                    %if &VKSflag ne 0                         %then %do; VKS     %end;
18139 +                %end;
18140 +                %else %do;
18141 +                    %if &ASEflag ne 0                         %then %do; _ASE_   %end;
18142 +                    %if &MISCflag ne 0  and "&_misc_" ne "."  %then %do; _MISC_  %end;
18143 +                    %if &KSflag ne 0                          %then %do; KS      %end;
18144 +                    %if &AURflag ne 0   and "&_aur_" ne "."   %then %do; _AUR_  %end;
18145 +                    %if &GINIflag ne 0  and "&_gini_" ne "."  %then %do; _GINI_  %end;
18146 +                %end;
18147 +               ;
18148 +            run;
18149 +        %end;
18150 +        %else %do;
18151 +            %goto skip;
18152 +        %end;
18153 +    %end;
18154 +    %else %if "&desc" eq "RULETABLE" %then %do;
18156 +        %let dsid = %sysfunc(open(&pd));
18157 +        %let printflag = 0;
18158 +        %if &dsid %then %do;
18159 +          %let printflag = %sysfunc(VARNUM(&dsid, EXP_CONF));
18160 +          %let lhandflag = %sysfunc(VARNUM(&dsid, _lhand));
18161 +          %let rhandflag = %sysfunc(VARNUM(&dsid, _rhand));
18162 +        %end;
18163 +        %if &dsid %then %let dsid=%sysfunc(close(&dsid));
18165 +        %EM_REPORT_TABLETEXT(subtitle=views_descriptions_&em_report_key_description);
18167 +        proc print data=&pd(obs=&maxobs) noobs label contents='';
18168 +            %if &printflag ne 0 %then %do;
18169 +                var rule exp_conf support lift count
18170 +                %if &lhandflag ne 0 %then %do; _lhand %end;
18171 +                %if &rhandflag ne 0 %then %do; _rhand %end;
18172 +                ;
18173 +            %end;
18174 +            %else %do;
18175 +                var rule conf support count
18176 +                %if &lhandflag ne 0 %then %do; _lhand %end;
18177 +                %if &rhandflag ne 0 %then %do; _rhand %end;
18178 +                ;
18179 +            %end;
18180 +        run;
18181 +    %end;
18182 +    %else %do ;
18183 +        %EM_REPORT_TABLETEXT(subtitle=views_descriptions_&em_report_key_description);
18184 +        proc print data=&pd(obs=&maxobs) noobs label contents=''; run ;
18185 +    %end ;
18186 +    %end;
18187 +%skip:
18188 +%mend EM_REPORT_DATA;
18190 +/*---------------------------------------------------------*/
18191 + * EM_REPORT_VARS ;
18192 + * EM_REPORT_VARS_FLIP ;
18193 + *     Report on variables used by the node ;
18194 +/*---------------------------------------------------------*/
18195 +%macro EM_REPORT_VARS_FLIP(data=,out=);
18197 +    proc sort data=&data;
18198 +        by descending role level name;
18199 +    run;
18201 +    data &out(keep=role level count names ) ;
18202 +        length role level $16 ;
18203 +        set &data end=eof ;
18204 +        length xrole role $32 count 8 names $200 ;
18205 +        krole= strip(role) !!' * ' !! strip(level) ;
18206 +        retain xrole ; if _n_ eq 1 then xrole=krole ;
18207 +        retain names ; if _n_ eq 1 then names=name ;
18208 +        retain count 0 ending 0 ;
18209 +        count+1 ;
18210 +        role=scan(xrole,1,'*') ;
18211 +        level=scan(xrole,2,'*') ;
18212 +        if xrole ne krole then do ;
18213 +            count=count-1 ;
18214 +            put _n_ "OUTPUT;" ;
18215 +            output ;
18216 +            xrole=krole ; names= strip(name) ;
18217 +            count=1  ; ending=0 ;
18218 +        end ;
18219 +        else if _n_ gt 1 then do ;
18220 +            if length(names) + length(name) lt 196 then
18221 +                names= strip(names) !! ' '  !! strip(name) ;
18222 +            else
18223 +            if not ending then do ;
18224 +                names= strip(names) !! ' ...' ;
18225 +                ending=1 ;
18226 +            end;
18227 +        end;
18228 +        role=scan(xrole,1,'*') ;
18229 +        level=scan(xrole,2,'*') ;
18230 +        if eof and count gt 0 then output ;
18231 +        label count = "%sysfunc(sasmsg(sashelp.dmine, rpt_count_vlabel, NOQUOTE)) "
18232 +              names = "%sysfunc(sasmsg(sashelp.dmine, rpt_varsel_label_name, NOQUOTE))"
18233 +              level = "%sysfunc(sasmsg(sashelp.dmine, rpt_level_vlabel, NOQUOTE))"
18234 +              role = "%sysfunc(sasmsg(sashelp.dmine, meta_role_vlabel, NOQUOTE))";
18235 +    run ;
18236 +%mend EM_REPORT_VARS_FLIP ;
18238 +%macro EM_REPORT_VARS(varlist=0);
18240 +    %if not %sysfunc(exist(&EM_REPORT_LIB..&EM_REPORT_NODEID._EMTRAINVARIABLE))
18241 +        %then %goto skip ;
18243 +    proc sql noprint;
18244 +        select count(*) into :tmp_numvar from &EM_REPORT_LIB..&EM_REPORT_NODEID._EMTRAINVARIABLE;
18245 +    quit;
18247 +    %if &tmp_numvar lt 1 %then %goto skip;
18248 +/*--- 1. get minimal set of vars -----------------------------------------*/
18250 +    data _tmpvl_pdv(drop=use newuse count) ;
18251 +        set &EM_REPORT_LIB..&EM_REPORT_NODEID._EMTRAINVARIABLE end=eof;
18252 +        length use newuse $1;
18253 +        label
18254 +            name   = "%sysfunc(sasmsg(sashelp.dmine, rpt_varsel_label_name, NOQUOTE))"
18255 +            level  = "%sysfunc(sasmsg(sashelp.dmine, rpt_level_vlabel, NOQUOTE))"
18256 +            role   = "%sysfunc(sasmsg(sashelp.dmine, meta_role_vlabel, NOQUOTE))"
18257 +            length = "%sysfunc(sasmsg(sashelp.dmine, meta_length_vlabel, NOQUOTE))"
18258 +        ;
18259 +        if use eq "" then use="Y";
18260 +        newuse=use;
18261 +        if use eq "D" then do;
18262 +            if role="REJECT" then newuse="N";
18263 +            else if role="INPUT"    then newuse="Y";
18264 +            else if role="CROSSID"  then newuse="Y";
18265 +            else if role="TEXT"     then newuse="Y";
18266 +        end ;
18267 +        if newuse eq "Y" then do ;
18268 +            count+1 ;
18269 +            output;
18270 +        end;
18271 +        if eof then call symput("varcount",put(count,6.)) ;
18272 +    run;
18274 +    proc sort data=_tmpvl_pdv; by name; run ;
18276 +/*--- 2. Summarize the variables -----------------------------------------*/
18278 +    %if &varlist ne 1 or &varcount gt &EM_REPORT_PRINT_MAXVARS %then %do;
18280 +        /* why not update pdv here? mxx */
18281 +        %EM_REPORT_VARS_FLIP(data=_tmpvl_pdv,out=_tmpvl_pds);
18282 +        %EM_REPORT_TABLETEXT(subtitle=rpt_varsummary_title);
18283 +        proc print data=_tmpvl_pds noobs label contents=''; run;
18285 +    %end;
18286 +    %else %do;
18288 +        %if %sysfunc(exist(&EM_REPORT_LIB..&EM_REPORT_NODEID._variableset)) %then %do;
18289 +            proc sort data=&EM_REPORT_LIB..&EM_REPORT_NODEID._variableset out=_tmpvl_vs;
18290 +                by name;
18291 +            data _tmpvl_pdv;
18292 +                update _tmpvl_pdv _tmpvl_vs;
18293 +                by name;
18294 +                label
18295 +                    type    = "%sysfunc(sasmsg(sashelp.dmine, meta_type_vlabel, NOQUOTE))"
18296 +                    format  = "%sysfunc(sasmsg(sashelp.dmine, meta_format_vlabel, NOQUOTE))"
18297 +                    creator = "%sysfunc(sasmsg(sashelp.dmine, meta_creator_vlabel, NOQUOTE))"
18298 +                    family  = "%sysfunc(sasmsg(sashelp.dmine, rpt_family_vlabel, NOQUOTE))"
18299 +                    label   = "%sysfunc(sasmsg(sashelp.dmine, meta_label_vlabel, NOQUOTE))"
18300 +                ;
18301 +            run;
18302 +        %end;
18304 +        %EM_REPORT_TABLETEXT(subtitle=rpt_varlist_title);
18305 +        proc print data=_tmpvl_pdv noobs label contents='';
18306 +            var name label role level type length format creator;
18307 +        run;
18308 +    %end;
18310 +/*--- 3. for SVM node with EMSTAT table ----------------------------------*/
18312 +    %let emstat_exist = 0;
18313 +    %if %sysfunc(exist(&EM_REPORT_LIB..&EM_REPORT_NODEID._emstat)) %then %do;
18314 +        proc sql noprint;
18315 +            select count(*) into :emstat_use from &em_report_lib..&nodeid._emreport
18316 +                where key="DESCRIPTION" and value="EMSTAT" ;
18317 +        quit;
18318 +        %if &emstat_use gt 0 %then %let emstat_exist = 1;
18319 +    %end;
18321 +    %if &emstat_exist eq 1 %then %do;
18323 +        /* check newname column */
18324 +        %let newname_exist = 0;
18325 +        %let newobs = 0;
18326 +        %let tsdsid = %sysfunc(open(work.pdv));
18327 +        %if &tsdsid %then %do;
18328 +            %let tsobs = %sysfunc(ATTRN(&tsdsid, NOBS));
18329 +            %let newobs = %sysfunc(varnum(&tsdsid, NEWNAME));
18330 +            %if &tsobs > 0 AND  &newobs > 0 %then %let newname_exist =1;
18331 +        %end;
18332 +        %if &tsdsid %then %let tsdsid = %sysfunc(close(&tsdsid));
18334 +        %if &newname_exist eq 1 %then %do;
18335 +            data _tmpvl_pdv; set _tmpvl_pdv(drop=name); run;
18336 +            proc sort data=_tmpvl_pdv(rename=(newname=NAME)); by name; run;
18337 +        %end;
18338 +        %else %do;
18339 +            proc sort data=_tmpvl_pdv; by name; run;
18340 +        %end;
18342 +        proc sort data=&EM_REPORT_LIB..&EM_REPORT_NODEID._VARIABLESET out=_tmpvl_v; by name; run;
18344 +        data _tmpvl_pdv ;
18345 +            update _tmpvl_pdv _tmpvl_v;
18346 +            by name;
18347 +            label label="%sysfunc(sasmsg(sashelp.dmine, meta_label_vlabel, NOQUOTE))";
18348 +        run;
18350 +        proc sort data=_tmpvl_pdv; by name; run;
18351 +        proc sort data=&EM_REPORT_LIB..&EM_REPORT_NODEID._EMSTAT out=_tmpvl_tempsort; by name; run;
18353 +        data _tmpvl_pdv;
18354 +            merge _tmpvl_pdv(keep=name label role level type length format creator family) _tmpvl_tempsort;
18355 +            by name;
18356 +            label type    = "%sysfunc(sasmsg(sashelp.dmine, meta_type_vlabel, NOQUOTE))"
18357 +                  format  = "%sysfunc(sasmsg(sashelp.dmine, meta_format_vlabel, NOQUOTE))"
18358 +                  creator = "%sysfunc(sasmsg(sashelp.dmine, meta_creator_vlabel, NOQUOTE))"
18359 +                  family  = "%sysfunc(sasmsg(sashelp.dmine, rpt_family_vlabel, NOQUOTE))";
18360 +        run;
18362 +        proc sort data=_tmpvl_pdv; by descending role name; run;
18364 +        %EM_REPORT_TABLETEXT(subtitle=rpt_variable_interval_title);
18365 +        proc print data=_tmpvl_pdv(where=(LEVEL="INTERVAL")) noobs label contents='';
18366 +            var name label role level type length format creator pctmiss min max mean std skewness kurtosis;
18367 +        run;
18369 +        %EM_REPORT_TABLETEXT(subtitle=rpt_variable_class_title);
18370 +        proc print data=_tmpvl_pdv(where=(LEVEL^="INTERVAL")) noobs label contents='';
18371 +            var name label role level type length format creator nlevel pctmiss;
18372 +        run;
18373 +    %end;
18375 +/*--- 4. now get vars created by this node -------------------------------*/
18377 +    %if not %sysfunc(exist(&EM_REPORT_LIB..&EM_REPORT_NODEID._CMETA_TRAIN))
18378 +        %then %goto skip;
18380 +    %let pdvxnum = 0;
18382 +    data _tmpvl_pdvx;
18383 +        set &EM_REPORT_LIB..&EM_REPORT_NODEID._CMETA_TRAIN end=eof ;
18384 +        where upcase(creator) eq "&EM_REPORT_NODEID" ;
18385 +        if eof then call symput('pdvxnum',put(_N_,6.));
18386 +        label
18387 +            name   = "%sysfunc(sasmsg(sashelp.dmine, rpt_varsel_label_name, NOQUOTE))"
18388 +            label  = "%sysfunc(sasmsg(sashelp.dmine, meta_label_vlabel, NOQUOTE))"
18389 +            role   = "%sysfunc(sasmsg(sashelp.dmine, meta_role_vlabel, NOQUOTE))"
18390 +            level  = "%sysfunc(sasmsg(sashelp.dmine, rpt_level_vlabel, NOQUOTE))"
18391 +            type   = "%sysfunc(sasmsg(sashelp.dmine, rpt_type_vlabel, NOQUOTE))"
18392 +            length = "%sysfunc(sasmsg(sashelp.dmine, meta_length_vlabel, NOQUOTE))";
18393 +    run;
18395 +    %if pdvxnum gt 0 %then %do;
18396 +        %if &varlist ne 1 or &pdvxnum gt &EM_REPORT_PRINT_MAXVARS %then %do ;
18398 +            %EM_REPORT_VARS_FLIP(data=_tmpvl_pdvx,out=_tmpvl_pdvxp) ;
18400 +            proc sql noprint;
18401 +                select count(*) into :nobs from _tmpvl_pdvxp;
18402 +            quit;
18404 +            %if &nobs > 0 %then %do;
18405 +                %EM_REPORT_TABLETEXT(subtitle=rpt_createvarsummary_title);
18406 +                proc print data=_tmpvl_pdvxp noobs label contents=''; run ;
18407 +            %end;
18408 +        %end;
18409 +        %else %do ;
18411 +            %EM_REPORT_TABLETEXT(subtitle=rpt_createvarslist_title);
18412 +            proc print data=_tmpvl_pdvx noobs label contents='';
18413 +                var name label role type length ;
18414 +            run;
18415 +        %end;
18416 +    %end;
18418 +/*------------------------------------------------------------------------*/
18420 +    proc datasets lib=work nolist;
18421 +        delete _tmpvl_:;
18422 +    run; quit;
18423 +    %skip:
18425 +%mend EM_REPORT_VARS;
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMUTIL.REPORTER_RPMMACROS.SOURCE.
18427 +%macro EM_REPORT_validSummaryPath(startNodeID =);
18428 +/*--- purpose: detect whether a flow is valid as a summary path ---*/
18430 +    %global EM_REPORT_NODES EM_REPORT_LIB RPM_REPORT_MODELNODE  RPM_REPORT_SCORENODE RPM_REPORT_MDLCOMP RPM_REPORT_VALIDSUMMARY;
18431 +    %local prednode em_report_mdlOutfit;
18432 +    %let RPM_REPORT_MODELNODE    = ;
18433 +    %let RPM_REPORT_SCORENODE    = ;
18434 +    %let RPM_REPORT_MDLCOMP      = ;
18435 +    %let RPM_REPORT_VALIDSUMMARY = 0;
18437 +    %if &EM_REPORT_NODES eq SUMMARY %then %do;
18439 +        %let predNode=;
18440 +        data _null_ ;
18441 +            set &EM_REPORT_LIB..em_dgraph(where=(to="&startNodeID"));
18442 +            call symput('predNode', strip(from));
18443 +        run;
18444 +        %let predNode = &predNode;
18446 +        /* if predecessor [to Reporter node] is MDLCOMP, RPM report is valid                           [MDLCOMP->Reporter]*/
18447 +        /* or if predecessor is SCORE and MODEL node is fed into score, RPM report is valid            [Score->Model->Reporter]*/
18448 +        /* or if MDLCOMP to SCORE to REPORTER, RPM report is valid, commented xxm                      [MDLCOMP->Score->Reporter]*/
18449 +        /* or if predecessor is MODEL node, RPM report is valid                                        ["model"->Reporter]*/
18450 +        /* or MDLCOMP->SCORE->CODEXPT->Reporter is valid*/
18452 +        %if %upcase(%substr(&prednode,1,7)) eq MDLCOMP %then %do;
18453 +            %let RPM_REPORT_MDLCOMP = &predNode;
18454 +            %let RPM_REPORT_VALIDSUMMARY=1;
18455 +            data _null_;
18456 +                set &EM_REPORT_LIB..&RPM_REPORT_MDLCOMP._emoutfit(where=(USE='Y'));
18457 +                call symput('RPM_REPORT_MODELNODE',strip(model));
18458 +            run;
18459 +        %end;
18460 +        %else %if %upcase(%substr(&prednode,1,5)) eq SCORE %then %do;
18461 +            %let RPM_REPORT_SCORENODE = &prednode;
18462 +            %let prednode = ;
18463 +            data _null_ ;
18464 +                set &EM_REPORT_LIB..em_dgraph(where=(to="&RPM_REPORT_SCORENODE"));
18465 +                call symput('predNode', strip(from));
18466 +            run;
18467 +            %let predNode = &predNode;
18469 +            %if &prednode ne %then %do;
18470 +                %if %upcase(%substr(&prednode,1,7)) eq MDLCOMP %then %do;
18471 +                    %let RPM_REPORT_MDLCOMP = &prednode;
18472 +                    %let RPM_REPORT_VALIDSUMMARY=1;
18473 +                    data _null_;
18474 +                        set &EM_REPORT_LIB..&RPM_REPORT_MDLCOMP._emoutfit(where=(USE='Y'));
18475 +                        call symput('RPM_REPORT_MODELNODE',strip(model));
18476 +                    run;
18477 +                %end;
18478 +                %else %do;
18479 +                    %let em_report_mdlOutfit = &EM_REPORT_LIB..&prednode._emoutfit;
18480 +                    %if %sysfunc(exist(&em_report_mdlOutfit))
18481 +                    %then %do;
18482 +                        %let RPM_REPORT_MODELNODE = &prednode;
18483 +                        %let RPM_REPORT_VALIDSUMMARY=1;
18484 +                    %end;
18485 +                %end;
18486 +            %end;
18487 +        %end;
18488 +        %else %if %upcase(%substr(&prednode,1,7)) eq CODEXPT %then %do;
18489 +            %let RPM_REPORT_SCOREXPTNODE = &prednode;
18490 +            %let prednode = ;
18491 +            data _null_ ;
18492 +                set &EM_REPORT_LIB..em_dgraph(where=(to="&RPM_REPORT_SCOREXPTNODE"));
18493 +                call symput('predNode', strip(from));
18494 +            run;
18495 +/*            %let predNode = &predNode;*/
18497 +            %if &prednode ne %then %do;
18498 +                %if %upcase(%substr(&prednode,1,5)) eq SCORE %then %do;
18499 +                    %let RPM_REPORT_SCORENODE = &prednode;
18500 +                    %let prednode = ;
18501 +                    data _null_ ;
18502 +                        set &EM_REPORT_LIB..em_dgraph(where=(to="&RPM_REPORT_SCORENODE"));
18503 +                        call symput('predNode', strip(from));
18504 +                    run;
18505 +/*                    %let predNode = &predNode; */
18507 +                    %if &prednode ne %then %do;
18508 +                        %if %upcase(%substr(&prednode,1,7)) eq MDLCOMP %then %do;
18509 +                            %let RPM_REPORT_MDLCOMP = &prednode;
18510 +                            %let RPM_REPORT_VALIDSUMMARY=1;
18511 +                            data _null_;
18512 +                                set &EM_REPORT_LIB..&RPM_REPORT_MDLCOMP._emoutfit(where=(USE='Y'));
18513 +                                call symput('RPM_REPORT_MODELNODE',strip(model));
18514 +                            run;
18515 +                        %end;
18516 +                    %end;
18517 +                %end;
18518 +            %end;
18519 +        %end;
18520 +        %else %do;
18521 +            %let em_report_mdlOutfit = &em_lib..&prednode._emoutfit;
18522 +            %if %sysfunc(exist(&em_report_mdlOutfit))
18523 +            %then %do;
18524 +                %let RPM_REPORT_MODELNODE = &prednode;
18525 +                %let RPM_REPORT_VALIDSUMMARY=1;
18526 +            %end;
18527 +        %end;
18528 +    %end;
18530 +    %let RPM_REPORT_MODELNODE = &RPM_REPORT_MODELNODE;
18531 +    %let RPM_REPORT_SCORENODE = &RPM_REPORT_SCORENODE;
18532 +    %let RPM_REPORT_MDLCOMP   = &RPM_REPORT_MDLCOMP;
18534 +%mend EM_REPORT_validSummaryPath;
18536 +%macro em_report_summary(runHP=0);
18538 +%global EM_REPORT_SUMMARY_TARGET RPM_REPORT_MDLCOMP RPM_REPORT_MODELNODE;
18539 +%local  IDSNode freqvar;
18541 +%if not %sysfunc(exist(emreportpath)) %then %goto skip ;
18543 +/*--- RPM-1. Identify the modeling node -------------------------------------*/
18545 +%let em_report_modelID = ;
18546 +%let em_report_modeldesc= ;
18548 +%if &RPM_REPORT_MDLCOMP ne %then %do;
18549 +    %let em_report_mdloutfit = &em_lib..&RPM_REPORT_MDLCOMP._emoutfit;
18550 +    %if %sysfunc(exist(&em_report_mdloutfit)) %then %do;
18551 +        data _null_;
18552 +           set &em_lib..&RPM_REPORT_MDLCOMP._emoutfit(where=(USE='Y'));
18553 +           call symput('em_report_modelID',   strip(model));
18554 +           call symput('em_report_modeldesc', strip(modeldescription));
18555 +        run;
18556 +    %end;
18557 +%end;
18558 +%else %if &RPM_REPORT_MODELNODE ne %then %do;
18559 +    %let em_report_mdloutfit = &em_lib..&RPM_REPORT_MODELNODE._emoutfit;
18560 +    %if %sysfunc(exist(&em_report_mdloutfit)) %then %do;
18561 +        %let em_report_modelID   = &RPM_REPORT_MODELNODE;
18562 +        %let em_report_modeldesc = ;
18563 +    %end;
18564 +%end;
18566 +/*--- RPM-2. Retrieve necessary information for the model--------------------*/
18568 +%if &em_report_modelId ne %then %do;
18570 +    /* RPM-2.1 save model information */
18572 +    %EM_GETNAME(key=MODELINFO, type=DATA);
18573 +    data &EM_USER_MODELINFO;
18574 +        format modelID $100. modeldesc $200.;
18575 +        modelid = "&em_report_modelID";
18576 +        modeldesc="&em_report_modeldesc";
18577 +    run;
18579 +    /* RPM-2.2 retrieve warning file if it exists */
18581 +    %let temp = %nrbquote(%sysfunc(PATHNAME(&em_report_lib)));
18582 +    %let path = %nrbquote(%sysfunc(tranwrd(&temp, %str(%"),"")));
18583 +    %let file = EMWARN.sas;
18584 +    %let warnfile = %nrbquote(&path&em_dsep&em_report_modelId&em_dsep&file);
18586 +    %if "&warnfile" ne "" %then %do;
18587 +        filename x "&warnfile" encoding='utf-8' NOBOM;
18588 +        data _null_;
18589 +            length string $1000;
18590 +            file PRINT;
18591 +            fid = fopen('x');
18592 +            do while (^fread(fid));
18593 +                rc= fget(fid, string, frlen(fid));
18594 +                string = tranwrd(string, "'", "''");
18595 +                if string eq '' then string = ' ';
18596 +                put string;
18597 +            end;
18598 +            fid = fclose(fid);
18599 +            put " ";
18600 +        run;
18601 +        filename x;
18602 +    %end;
18604 +    /* RPM-2.3 retrieve ID nodeid */
18606 +    data _null_;
18607 +        set emreportpath(where=(substr(upcase(strip(nodeid)),1,3) eq "IDS"));
18608 +        call symput("idsnode",strip(nodeid)) ;
18609 +    run;
18611 +        data _null_;
18612 +        set emreportpath(where=(substr(upcase(strip(nodeid)),1,7) eq "FIMPORT"));
18613 +        call symput("idsnode",strip(nodeid)) ;
18614 +        run;
18616 +%let fimnode=;
18617 +%if "%substr(%upcase(%sysfunc(strip(&idsnode))),1,7)" eq "FIMPORT" %then %do;
18618 +    %let idscmeta = &em_lib..&idsnode._emtrainvariable;
18619 +    %let idstmeta = &em_lib..&idsnode._emtmetadefault;
18620 +    %let fimnode = &idsnode;
18621 +    %let idsnode = &em_report_modelID;
18622 +%end;
18623 +%else %do;
18624 +    %let idscmeta = &em_lib..&idsnode._emcmetadefault;
18625 +    %let idstmeta = &em_lib..&idsnode._emtmetadefault;
18626 +%end;
18628 +    %if %sysfunc(exist(&idstmeta)) %then %do;
18629 +        data _null_;
18630 +            set  &idstmeta;
18631 +            call symput('rpt_rpm_data',    strip(MEMNAME));
18632 +            call symput('rpt_rpm_datalib', strip(LIBNAME));
18633 +            call symput('rpt_rpm_nobs',    strip(NOBS));
18634 +            call symput('rpt_rpm_ncols',   strip(NCOLS));
18635 +        run;
18636 +    %end;
18638 +    /* RPM-2.4 retrieve name of target to use in presense of multiple targets */
18639 +    /* prefer to use the target given by &EM_REPORT_SUMMARY_TARGET        */
18640 +    /* otherwise the last target modelled (by name) will be used          */
18642 +    %let mdlTarget = ;
18644 +    %if "&EM_REPORT_SUMMARY_TARGET" ne "" %then %do;
18645 +        proc sql noprint;
18646 +            select count(*) into :tmpn
18647 +            from &em_lib..&em_report_modelid._variableset;
18648 +                where name eq "&EM_REPORT_SUMMARY_TARGET" and upcase(role) eq "TARGET";
18649 +        quit;
18650 +        %if &tmpn eq 1 %then %let mdlTarget = &EM_REPORT_SUMMARY_TARGET;
18651 +    %end;
18653 +    %if "&mdlTarget" eq "" %then %do;
18654 +        data _null_;
18655 +            set &em_lib..&em_report_modelid._variableset(where=(role="TARGET" and use="Y"));
18656 +            call symput('mdlTarget', strip(name));
18657 +        run;
18658 +    %end;
18660 +    /* RPM-2.5 retrieve name of decmeta from eminfo of the chosen model */
18662 +    data _null_;
18663 +        set &em_lib..&em_report_modelid._eminfo;
18664 +        if KEY="DECMETA" %if "&mdlTarget" ne "" %then %do; and target="&mdlTarget" %end;
18665 +            then  call symput('rpt_rpm_mdldecmeta', strip(data));
18666 +        if KEY="DECDATA" %if "&mdlTarget" ne "" %then %do; and target="&mdlTarget" %end;
18667 +            then  call symput('rpt_rpm_mdldecdata', strip(data));
18668 +    run;
18670 +    /* RPM-2.6 retrieve name of DECISION variable if it exists      */
18671 +    /*       - use this as target var for categorical target        */
18672 +    /*       - otherwise use INTO variable as class target var      */
18673 +    /* RPM-2.7 retrieve target event level, save TARGETINFO table   */
18674 +    /* RPM-2.8 retrieve name of the PREDICTED variable from decmeta */
18675 +    /*       - INTERVAL TARGET ONLY                                 */
18677 +    %let catTarget = ;
18679 +    data _null_;
18680 +        set &rpt_rpm_mdldecmeta;
18681 +        if _type_ eq "INTO"          then call symput('catTarget', strip(variable));
18682 +        if _type_ eq "MODELDECISION" then call symput('catTarget', strip(variable));
18684 +        if _type_ eq "TARGET" then do;
18685 +            call symput('rpt_rpm_eventlvl',    strip(event));
18686 +            call symput('rpt_rpm_targetName',  strip(variable));
18687 +            call symput('rpt_rpm_targetLevel', strip(level));
18688 +            call symput('rpt_rpm_targetLabel', strip(label));
18689 +        end;
18690 +    data _null_;
18691 +        set &rpt_rpm_mdldecmeta;
18692 +        %if "&rpt_rpm_targetLevel" eq "INTERVAL" %then %do;
18693 +            if _type_ eq "PREDICTED"
18694 +                then call symput('rpt_rpm_predvar', strip(variable));
18695 +        %end;
18696 +    run;
18698 +    %EM_GETNAME(key=TARGETINFO, type=DATA);
18699 +    data &EM_USER_TARGETINFO;
18700 +        set &rpt_rpm_mdldecmeta;
18701 +    run;
18703 +    /* RPM-2.9 retrieve freq variable name if it exists */
18705 +    %let freqvar=;
18706 +    data _null_;
18707 +        set &em_lib..&em_report_modelid._variableset(where=(role eq "FREQ"));
18708 +        call symput('freqvar', strip(name));
18709 +    run;
18711 +    /* RPM-2.10 retrieve listing of all inputs into the model */
18712 +    /*     process only when SCORENODE exists                 */
18714 +    %if &RPM_REPORT_SCORENODE ne %then %do;
18716 +        %EM_REGISTER(key=tempvar,  type=DATA);
18717 +        %EM_REGISTER(key=tempmeta, type=DATA);
18718 +        %EM_GETNAME( key=tempvar,  type=DATA);
18719 +        %EM_GETNAME( key=tempmeta, type=DATA);
18721 +        data &EM_USER_tempvar;
18722 +            %if %sysfunc(exist(&em_lib..&rpm_report_scorenode._eminputvars)) %then %do;
18723 +                set &em_lib..&rpm_report_scorenode._eminputvars(where=(scorevar="Y"));
18724 +            %end;
18725 +            %else %do;
18726 +                set &em_lib..&idsnode._variableset(where=(upcase(role)='INPUT') keep=role name);
18727 +            %end;
18728 +        run;
18730 +        proc sql noprint;
18731 +            select count(distinct name) into :n_svars from &EM_USER_tempvar;
18732 +        quit;
18734 +        proc sort data=&EM_USER_tempvar;       by name; run;
18736 +%if %sysfunc(exist(&idscmeta)) %then %do;
18737 +        proc sort data=&idscmeta out=tempmeta; by name; run;
18738 +        data &EM_USER_tempmeta;
18739 +            merge &EM_USER_tempvar(in=_a) tempmeta;
18740 +            by name;
18741 +            if _a;
18742 +        run;
18743 +%end;
18744 +%else %do;
18745 +        data &EM_USER_tempmeta;
18746 +            set &EM_USER_tempvar;
18747 +            by name;
18748 +        run;
18749 +%end;
18751 +        proc delete data=tempmeta; run;
18752 +    %end;
18754 +    /* RPM-2.11 is HPRPM or not */
18756 +    proc sql noprint;
18757 +        %if "&fimnode" ne "" %then %do;
18758 +            select count(*) into :rpt_rpm_isHP from &em_lib..&em_report_modelid._eminfo
18759 +        %end;
18760 +        %else %do;
18761 +            select count(*) into :rpt_rpm_isHP from &em_lib..&idsnode._eminfo
18762 +        %end;
18763 +                where upcase(key) eq 'HPDMSAMPLE';
18764 +    quit;
18766 +    %if &rpt_rpm_isHP ne 0 %then %let rpt_rpm_isHP = 1;
18768 +    /* RPM-2.12 determine if vvn variables are present in ids node */
18770 +    %let vvnflag = 0;
18771 +    %let cmdsid = %sysfunc(open(&idscmeta));
18772 +    %if &cmdsid %then %do;
18773 +        %let newnum = %sysfunc(varnum(&cmdsid, newname));
18774 +        %if &newnum gt 0 %then %let vvnflag = 1;
18775 +    %end;
18776 +    %if &cmdsid %then %let cmdsid = %sysfunc(close(&cmdsid));
18778 +    %if &vvnflag eq 0
18779 +        %then %let name_to_use = name;
18780 +        %else %let name_to_use = newname;
18782 +/* end of 2. Retrieve necessary information for the model */
18783 +%end;
18785 +/*******************************************************************************************/
18787 +%if &em_report_modelId ne  and %sysfunc(exist(&idstmeta)) %then %do;
18789 +    %if &EM_PROPERTY_SUMMARIZATION eq Y %then %do;
18791 +        %EM_GETNAME(key=DATASUM,   type=DATA);
18792 +        %EM_GETNAME(key=TARGETSUM, type=DATA);
18793 +        %EM_GETNAME(key=VARSUM,    type=DATA);
18794 +        %let rpt_rpm_eventlvl = %nrbquote(&rpt_rpm_eventlvl);
18796 +        /* RPM-3.1 - output data summary ------------------------------------------------------------------*/
18798 +        data &EM_USER_DATASUM;
18799 +            length stat $200 value $200;
18800 +            label stat = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))"
18801 +                  value = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptvalue_vlabel, NOQUOTE))";
18803 +            stat  = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptinputdatasource_vlabel, NOQUOTE))";
18804 +            value = "&rpt_rpm_datalib..&rpt_rpm_data";
18805 +            output;
18807 +            stat = "%sysfunc(sasmsg(sashelp.dmine, rpt_rpttargetvariable_vlabel, NOQUOTE))";
18808 +            %if "%nrbquote(&rpt_rpm_targetLabel)" ne "" %then %do;
18809 +                value = "%nrbquote(&rpt_rpm_targetLabel)";
18810 +            %end;
18811 +            %else %do;
18812 +                value = "&rpt_rpm_targetName";
18813 +            %end;
18814 +            output;
18816 +            %if "&rpt_rpm_targetLevel" ne "INTERVAL" %then %do;
18817 +                stat  = "%sysfunc(sasmsg(sashelp.dmine, rpt_rpteventlevel_vlabel, NOQUOTE))";
18818 +                value = "&rpt_rpm_eventlvl";
18819 +                output;
18820 +            %end;
18822 +            stat  = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptobservations_vlabel, NOQUOTE))";
18823 +            value = "&rpt_rpm_nobs";
18824 +            output;
18826 +            stat = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptorigvars_vlabel, NOQUOTE))";
18827 +            value = "&rpt_rpm_ncols";
18828 +            output;
18830 +            %if &RPM_REPORT_SCORENODE ne %then %do;
18831 +                %if &n_svars ne %then %do;
18832 +                    stat="%sysfunc(sasmsg(sashelp.dmine, rpt_rptselectedvars_vlabel, NOQUOTE))";
18833 +                    value="&n_svars";
18834 +                    output;
18835 +                %end;
18836 +            %end;
18837 +        run;
18839 +        ods &EM_REPORT_FORMAT text=" ";
18840 +        ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptmodelsummarization_title, NOQUOTE))";
18841 +        ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptdata_title, NOQUOTE))";
18842 +        ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptmodelsummarization_title, NOQUOTE)) %sysfunc(sasmsg(sashelp.dmine, rpt_rptdata_title, NOQUOTE))";
18844 +        proc print data=&EM_USER_DATASUM noobs label style=[outputwidth=50%]; run;
18846 +        /* RPM-3.2 - output target summary -----------------------------------------------------------------*/
18848 +       ods &EM_REPORT_FORMAT text=" ";
18849 +       %if "%nrbquote(&rpt_rpm_targetLabel)" ne "" %then %do;
18850 +           ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rpttarget_title, NOQUOTE, %nrstr(&rpt_rpm_targetLabel)))";
18851 +           ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rpttarget_title, NOQUOTE, %nrstr(&rpt_rpm_targetLabel)))";
18852 +       %end;
18853 +       %else %do;
18854 +           ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rpttarget_title, NOQUOTE, &rpt_rpm_targetName))";
18855 +           ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rpttarget_title, NOQUOTE, &rpt_rpm_targetName))";
18856 +       %end;
18858 +        %if "&rpt_rpm_targetLevel" ne "INTERVAL"
18859 +        %then %do;
18860 +            /* target summary for for non-interval target */
18862 +            %let useD = N;
18863 +            data _null_;
18864 +                set &rpt_rpm_mdldecmeta(where=(_type_ in ('MATRIX'))) end=eof;
18865 +                if eof then call symput('useD', USE);
18866 +            run;
18868 +            %let useP = N;
18869 +            data _null_;
18870 +                set &rpt_rpm_mdldecmeta(where=(_type_ in ('DECPRIOR'))) end=eof;
18871 +                if eof then call symput('useP', USE);
18872 +            run;
18874 +            data &EM_USER_TARGETSUM;
18875 +                set &rpt_rpm_mdldecdata;
18876 +                trainPrior = trainPrior * 100;
18877 +                label
18878 +                    &rpt_rpm_targetName = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptvalue_vlabel, NOQUOTE))"
18879 +                    count      = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptcount_vlabel, NOQUOTE))"
18880 +                    trainprior = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptdatapercentage_vlabel, NOQUOTE))"
18881 +                    decprior   = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptadjustedperc_vlabel, NOQUOTE))";
18882 +                ;
18883 +                %if &useD eq Y and &useP eq Y %then %do; drop dataprior;          %end;
18884 +                %if &useD eq Y and &useP eq N %then %do; drop decprior dataprior; %end;
18885 +                %if &useD eq N and &useP eq Y %then %do; keep &rpt_rpm_targetName count trainprior decprior; %end;
18886 +                %if &useD eq N and &useP eq N %then %do; keep &rpt_rpm_targetName count trainprior;          %end;
18887 +            run;
18889 +            proc print data=&EM_USER_TARGETSUM noobs label style=[outputwidth=50%]; run;
18890 +        %end;
18891 +        %else %do;
18892 +            /* target summary for interval target */
18894 +            data _null_;
18895 +                %if "&fimnode" ne "" %then %do;
18896 +                    set  &idscmeta(where=(ROLE='TARGET'));
18897 +                %end;
18898 +                %else %do;
18899 +                    set  &em_lib..&idsnode._cmeta_data(where=(ROLE='TARGET'));
18900 +                %end;
18901 +                    call symput('rpt_rpm_ids_target',    strip(name));
18902 +            run;
18904 +            %if &rpt_rpm_isHP eq 1 %then %do;
18905 +                ods &EM_REPORT_FORMAT exclude all;
18906 +                ods listing;
18907 +                proc hpdmdb data=&em_lib..&idsnode._data varout=temp1;
18908 +                    var &rpt_rpm_ids_target;
18909 +                proc transpose data=temp1
18910 +                    out=&EM_USER_TARGETSUM(rename=(col1=&rpt_rpm_ids_target _name_=_stat_));
18911 +                    by name notsorted;
18912 +                    var n min max mean std;
18913 +                run;
18914 +                ods listing close;
18915 +                ods &EM_REPORT_FORMAT select all;
18916 +                proc delete data=temp1; run;
18917 +            %end;
18918 +            %else %do;
18919 +                %if "&fimnode" ne "" %then %do;
18920 +                    proc means data=&em_lib..&fimnode._data noprint;
18921 +                %end;
18922 +                %else %do;
18923 +                    proc means data=&em_lib..&idsnode._data noprint;
18924 +                %end;
18925 +                    var &rpt_rpm_ids_target;
18926 +                    output out=&EM_USER_TARGETSUM;
18927 +                run;
18929 +                data &EM_USER_TARGETSUM;
18930 +                    format _stat_ $50.;
18931 +                    set &EM_USER_TARGETSUM;
18932 +                    if strip(upcase(_stat_)) eq "MIN"  then _stat_ = "%sysfunc(sasmsg(sashelp.dmine, rpt_minimum_vlabel, NOQUOTE))";
18933 +                    if strip(upcase(_stat_)) eq "MAX"  then _stat_ = "%sysfunc(sasmsg(sashelp.dmine, rpt_maximum_vlabel, NOQUOTE))";
18934 +                    if strip(upcase(_stat_)) eq "N"    then _stat_ = "%sysfunc(sasmsg(sashelp.dmine, rpt_numObs_vlabel, NOQUOTE))";
18935 +                    if strip(upcase(_stat_)) eq "MEAN" then _stat_ = "%sysfunc(sasmsg(sashelp.dmine, rpt_mean_vlabel, NOQUOTE))";
18936 +                    if strip(upcase(_stat_)) eq "STD"  then _stat_ = "%sysfunc(sasmsg(sashelp.dmine, rpt_std_vlabel, NOQUOTE))";
18937 +                run;
18938 +            %end;
18940 +            proc print data=&EM_USER_TARGETSUM noobs label style=[outputwidth=50%];
18941 +                var _stat_ &rpt_rpm_ids_target;
18942 +                label _stat_        = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptstatistic_vlabel, NOQUOTE))"
18943 +                &rpt_rpm_ids_target = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptvalue_vlabel, NOQUOTE))";
18944 +            run;
18945 +        %end;
18947 +        /* RPM-3.3 - output variable summary ---------------------------------------------------------------*/
18949 +        %if &RPM_REPORT_SCORENODE ne %then %do;
18951 +            proc sql noprint;
18952 +                create table tempselected as
18953 +                    select a.*, b.name as selected
18954 +                    from &em_lib..&idsnode._variableset as a left join &EM_USER_tempmeta as b
18955 +                        on a.name eq b.name
18956 +                ;
18957 +                create table &EM_USER_VARSUM as
18958 +                    select role,level, count(distinct name) as count, sum(selected ne ' ') as sel_count
18959 +                    from tempselected
18960 +                    group by role, level
18961 +                    order by role, level
18962 +                ;
18963 +            quit;
18965 +            ods &EM_REPORT_FORMAT text=" ";
18966 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptvarsummary_title, NOQUOTE))";
18967 +            ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptvarsummary_title, NOQUOTE))";
18969 +            data &EM_USER_VARSUM;
18970 +                format role $50. level $50.;
18971 +                set &EM_USER_VARSUM;
18973 +                role  = upcase(strip(role));
18974 +                level = upcase(strip(level));
18976 +                     if role eq 'ASSESS'         then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_assess_vlabel, NOQUOTE))";
18977 +                else if role eq 'COST'           then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_cost_vlabel, NOQUOTE))";
18978 +                else if role eq 'CENSOR'         then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_censor_vlabel, NOQUOTE))";
18979 +                else if role eq 'CROSSID'        then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_crossid_vlabel, NOQUOTE))";
18980 +                else if role eq 'FREQ'           then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_freq_vlabel, NOQUOTE))";
18981 +                else if role eq 'GROUP'          then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_group_vlabel, NOQUOTE))";
18982 +                else if role eq 'DECISION'       then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_decision_vlabel, NOQUOTE))";
18983 +                else if role eq 'CLASSIFICATION' then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_classification_vlabel, NOQUOTE))";
18984 +                else if role eq 'KEY'            then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_key_vlabel, NOQUOTE))";
18985 +                else if role eq 'MISSING'        then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_missing_vlabel, NOQUOTE))";
18986 +                else if role eq 'PREDICT'        then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_predict_vlabel, NOQUOTE))";
18987 +                else if role eq 'REFERRER'       then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_referrer_vlabel, NOQUOTE))";
18988 +                else if role eq 'REJECTED'       then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_rejected_vlabel, NOQUOTE))";
18989 +                else if role eq 'SEGMENT'        then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_segment_vlabel, NOQUOTE))";
18990 +                else if role eq 'SEQUENCE'       then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_sequence_vlabel, NOQUOTE))";
18991 +                else if role eq 'TEXT'           then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_text_vlabel, NOQUOTE))";
18992 +                else if role eq 'TIMEID'         then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_timeid_vlabel, NOQUOTE))";
18993 +                else if role eq 'TRIAL'          then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_trial_vlabel, NOQUOTE))";
18994 +                else if role eq 'ID'             then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_id_vlabel, NOQUOTE))";
18995 +                else if role eq 'INPUT'          then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_input_vlabel, NOQUOTE))";
18996 +                else if role eq 'TARGET'         then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_target_vlabel, NOQUOTE))";
18997 +                else if role eq 'TREATMENT'      then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_treatment_vlabel, NOQUOTE))";
18998 +                else if role eq 'RESIDUAL '      then role= "%sysfunc(sasmsg(sashelp.dmine, rpt_residual_vlabel, NOQUOTE))";
18999 +                else if role eq 'LABEL'          then role= "%sysfunc(sasmsg(sashelp.dmine, meta_label_vlabel, NOQUOTE))";
19001 +                     if level eq 'INTERVAL'  then level = "%sysfunc(sasmsg(sashelp.dmine, rpt_rpm_interval_vlabel , NOQUOTE))";
19002 +                else if level eq 'NOMINAL'   then level = "%sysfunc(sasmsg(sashelp.dmine, rpt_rpm_nominal_vlabel , NOQUOTE))";
19003 +                else if level eq 'ORDINAL'   then level = "%sysfunc(sasmsg(sashelp.dmine, rpt_rpm_ordinal_vlabel , NOQUOTE))";
19004 +                else if level eq 'BINARY'    then level = "%sysfunc(sasmsg(sashelp.dmine, rpt_rpm_binary_vlabel , NOQUOTE))";
19005 +                else if level eq 'UNARY'     then level = "%sysfunc(sasmsg(sashelp.dmine, rpt_rpm_unary_vlabel , NOQUOTE))";
19007 +                role  = strip(role);
19008 +                level = strip(level);
19009 +            run;
19011 +            proc print data=&EM_USER_VARSUM label noobs style=[outputwidth=50%];
19012 +                var role level count sel_count;
19013 +                label role      = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptrole_vlabel, NOQUOTE))"
19014 +                      level     = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptlevel_vlabel, NOQUOTE))"
19015 +                      count     = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptorigcount_vlabel, NOQUOTE))"
19016 +                      sel_count = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptselcount_vlabel, NOQUOTE))";
19017 +            run;
19019 +            proc delete data=tempselected; run;
19020 +        %end;
19021 +    %end;  /* end of --&EM_PROPERTY_SUMMARIZATION eq Y           --*/
19022 +/* three output in this section: datasum, targetsum, varsum      --*/
19023 +%end; /* end of &em_report_modelId ne - end of block 3.1,3.2,3.3 --*/
19026 +/***** RPM-4. varRank and Scorecard ************************************************************************/
19028 +%if &em_report_modelId ne  and %sysfunc(exist(&idstmeta)) and &RPM_REPORT_SCORENODE ne  %then %do;
19030 +    %EM_GETNAME(key=VARRANK,   type=DATA);
19031 +    %EM_GETNAME(key=SCORECARD, type=DATA);
19033 +    %if "&rpt_rpm_targetLevel" ne "INTERVAL"
19034 +        %then %let scorecard_target = &catTarget;
19035 +        %else %let scorecard_target = &rpt_rpm_predvar;
19037 +    %if &runHP eq 0 %then %do;
19039 +        %global EM_SUMMARY_REPORT_MAXOBS  EM_SUMMARY_REPORT_SAMPLEMETHOD ;
19041 +        %if &EM_SUMMARY_REPORT_MAXOBS ne  %then %do;
19042 +            %let maxobs = &EM_SUMMARY_REPORT_MAXOBS;
19043 +        %end;
19044 +        %else %do;
19045 +            %exploreObs(data=&em_lib..&em_report_modelid._train);
19046 +            %let maxobs = &_EXPLOREOBS_MAX;
19047 +        %end;
19049 +        %if "&EM_SUMMARY_REPORT_SAMPLEMETHOD" ne ""
19050 +            %then  %let sampleMethod = &EM_SUMMARY_REPORT_SAMPLEMETHOD;
19051 +            %else  %let sampleMethod = RANDOM;
19053 +        %exploreSample(
19054 +            data     = &em_lib..&em_report_modelid._train,
19055 +            seed     = 12345,
19056 +            output   = &em_report_modelid._trainsample,
19057 +            method   = &sampleMethod,
19058 +            size     = &maxobs,
19059 +            sizetype = obs,
19060 +            metaData = &em_lib..&em_report_modelid._cmeta_train
19061 +        );
19063 +         proc delete data = &EM_USER_ScoreCard;
19064 +         run;
19066 +        %em_reporter_scorecard(
19067 +            indata        = %str(&em_report_modelid._trainsample),
19068 +            indata2       = %str(&em_lib..&em_report_modelid._train),
19069 +            inVariableSet = &EM_USER_tempmeta,
19070 +            name          = &name_to_use,
19071 +            libname       = work,
19072 +            crosstab      = &EM_PROPERTY_CROSSTABS,
19073 +            targetVar     = &scorecard_target,
19074 +            targetVar2    = &rpt_rpm_targetName,
19075 +            targetLevel   = &rpt_rpm_targetLevel,
19076 +            freqVar       = &freqVar,
19077 +            outVarRank    = &EM_USER_VarRank,
19078 +            outScoreCard  = &EM_USER_ScoreCard
19079 +        );
19081 +        proc sort data=&em_lib..&idsnode._variableset
19082 +            out=templabel(keep=&name_to_use label %if &vvnflag eq 1 %then %do; name %end;);
19083 +            by &name_to_use;
19084 +        run;
19086 +        %if %sysfunc(exist(&EM_USER_ScoreCard)) ne 0 %then %do;
19088 +            proc sort data=&EM_USER_ScoreCard;
19089 +                by displayVar;
19090 +            data &EM_USER_ScoreCard;
19091 +                merge &EM_USER_ScoreCard(in=_a) templabel(rename=(&name_to_use=displayVar label=emrpmtmp_lbael));
19092 +                by displayVar;
19093 +                if _a;
19094 +                %if &vvnflag eq 1 %then %do;
19095 +                    if ^missing(name)  then displayvar=name;
19096 +                %end;
19097 +                if ^missing(emrpmtmp_lbael) then displayvar=emrpmtmp_lbael;
19098 +                drop emrpmtmp_lbael name;
19099 +            run;
19101 +        %end;
19103 +        %if %sysfunc(exist(&EM_USER_VarRank)) ne 0 %then %do;
19105 +            proc sort data=&EM_USER_VarRank;
19106 +                by variable;
19107 +            data &EM_USER_VarRank;
19108 +                merge &EM_USER_VarRank(in=_a) templabel(rename=(&name_to_use=variable));
19109 +                by variable;
19110 +                if _a;
19111 +                if missing(label) then do;
19112 +                    label = variable;
19113 +                    %if &vvnflag eq 1 %then %do;
19114 +                        if ^missing(name) then label=name;
19115 +                    %end;
19116 +                end;
19117 +            proc sort data=&EM_USER_VarRank;
19118 +                by descending rank;
19119 +            run;
19121 +        %end;
19123 +        %if %sysfunc(exist(&EM_USER_VarRank)) ne 0 and &EM_PROPERTY_VARRANKING eq Y %then %do;
19124 +            /* generate gtable like listing of variable importance */
19125 +            ods path(prepend) work.templat(update);
19126 +            proc template;
19127 +                define statgraph Rules / store=work.templat ;
19128 +                    begingraph / designheight=470px;
19129 +                        %if "&EM_REPORT_TITLE" ne ""
19130 +                            %then %do; entrytitle "&EM_REPORT_TITLE"; %end;
19131 +                            %else %do; entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))"; %end;
19132 +                        entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_rptvarranking_title, NOQUOTE))";
19133 +                        layout lattice / columns=2 rowdatarange=union columnweights=(0.9 0.1) columngutter=0;
19134 +                            rowaxes; rowaxis; endrowaxes;
19135 +                            layout overlay / xaxisopts=(display=(line));
19136 +                                barchart x=label y=worth / orient=horizontal;
19137 +                            endlayout;
19138 +                        endlayout;
19139 +                    endgraph;
19140 +                end;
19141 +            run;
19142 +            proc sgrender data=&EM_USER_VARRANK template=Rules; run;
19143 +        %end;
19144 +    %end;
19145 +    %else %do;
19147 +        %if &rpt_rpm_isHP eq 1
19148 +            %then %let scorecard_lib = &rpt_rpm_datalib;
19149 +            %else %let scorecard_lib = work;
19151 +        ods &EM_REPORT_FORMAT exclude all;
19152 +        ods listing;
19153 +        %hpdm_scorecard(
19154 +            indata        = %str(&em_lib..&em_report_modelID._train),
19155 +            inVariableSet = &EM_USER_tempmeta,
19156 +            name          = &name_to_use,
19157 +            libname       = &scorecard_lib,
19158 +            crosstab      = &EM_PROPERTY_CROSSTABS,
19159 +            targetVar     = &scorecard_target,
19160 +            targetVar2    = &rpt_rpm_targetName,
19161 +            targetLevel   = &rpt_rpm_targetLevel,
19162 +            freqVar       = &freqVar,
19163 +            outVarRank    = &EM_USER_VarRank,
19164 +            outScoreCard  = &EM_USER_ScoreCard
19165 +        );
19166 +        ods listing close;
19167 +        ods &EM_REPORT_FORMAT select all;
19169 +        /* 3.4 - Variable Ranking from PROC HPREDUCE */
19171 +        %if &EM_PROPERTY_VARRANKING eq Y %then %do;
19172 +            ods &EM_REPORT_FORMAT text=" ";
19173 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptvarranking_title, NOQUOTE))";
19174 +            ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptvarranking_title, NOQUOTE))";
19175 +            proc print data=&EM_USER_VARRANK noobs;
19176 +                var number variable;
19177 +            run;
19178 +        %end;
19179 +    %end;
19180 + %end;
19183 +/* RPM-5. Cumulative Lift chart *********************************************************************/
19185 +%if &EM_PROPERTY_LIFTCHART eq Y %then %do;
19187 +    %let em_rank = &em_lib..%sysfunc(trim(&em_report_modelid))_emrank;
19189 +    %if %sysfunc(exist(&em_rank)) %then %do;
19191 +        %EM_GETNAME(key=LIFT, type=DATA);
19192 +        data &EM_USER_LIFT;
19193 +            set &em_rank end=eof;
19194 +            if eof then call symput("_tarexists_", strip(target));
19196 +                 if upcase(strip(datarole)) eq 'TRAIN'    then datarole = "%sysfunc(sasmsg(sashelp.dmine, rpt_roletrain_value,    NOQUOTE))";
19197 +            else if upcase(strip(datarole)) eq 'RAW'      then datarole = "%sysfunc(sasmsg(sashelp.dmine, rpt_roleraw_value,      NOQUOTE))";
19198 +            else if upcase(strip(datarole)) eq 'VALIDATE' then datarole = "%sysfunc(sasmsg(sashelp.dmine, rpt_rolevalidate_value, NOQUOTE))";
19199 +            datarole = strip(datarole);
19200 +        run;
19202 +        %let em_class=&em_lib..%sysfunc(trim(&em_report_modelid))_emclassification;
19204 +        %if (%sysfunc(exist(&em_class)) and "&rpt_rpm_targetLevel" ne "INTERVAL" )
19205 +        %then %do;
19206 +            %let this_title = rpt_rptcumliftchart_title;
19207 +            %let this_plotY = liftc;
19208 +        %end;
19209 +        %else %do;
19210 +            %let this_title = rpt_rptmeanpredresp_title;
19211 +            %let this_plotY = _MEANP_;
19212 +        %end;
19214 +        ods path(prepend) work.templat(update);
19215 +        proc template;
19216 +            define statgraph liftplot / store=work.templat;
19217 +                begingraph / designheight=470px;
19218 +                    %if "&EM_REPORT_TITLE" ne ""
19219 +                        %then %do; entrytitle "&EM_REPORT_TITLE"; %end;
19220 +                        %else %do; entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))"; %end;
19221 +                    entrytitle "%sysfunc(sasmsg(sashelp.dmine, &this_title, NOQUOTE))";
19222 +                    layout overlay;
19223 +                        seriesplot x=decile y=&this_plotY
19224 +                            / group =datarole lineattrs=(pattern=solid) name='lift';
19225 +                        discretelegend 'lift' ;
19226 +                    endlayout;
19227 +                endgraph;
19228 +            end;
19229 +        run;
19231 +        proc sgrender data=&EM_USER_LIFT
19232 +            %if "&_tarexists_" ne "." %then %do; (where=(target="&rpt_rpm_targetName")) %end;
19233 +            template=liftplot;
19234 +        run;
19235 +    %end;
19236 +%end;
19238 +/* RPM-6. Cumulative captured response chart *********************************************************/
19240 +%if &EM_PROPERTY_BASICOUTPUT eq Y %then %do;
19242 +    %EM_GETNAME(key=CAPRESPONSE, type=DATA);
19244 +    %let em_report_rank = &em_lib..%sysfunc(trim(&em_report_modelid))_emrank;
19246 +    %if %sysfunc(exist(&em_report_rank)) %then %do;
19248 +        %let em_report_class = &em_lib..%sysfunc(trim(&em_report_modelid))_emclassification;
19250 +        %if (%sysfunc(exist(&em_report_class)) and "&rpt_rpm_targetLevel" ne "INTERVAL" ) %then %do;
19252 +            /* generate % values and create categorical x values for plotting purposes */
19253 +            data &EM_USER_CAPRESPONSE;
19254 +                set &em_report_rank end=eof;
19255 +                length baseCat bestCat capCat decCat liftCat liftCCat $5;
19256 +                format basecapcp bestcapcp capcp decilep PERCENT6.0;
19257 +                basecapcp = basecapc / 100;  baseCat= put(basecapcp, PERCENT6.0 -L);
19258 +                bestcapcp = bestcapc / 100;  bestCat= put(bestcapcp, PERCENT6.0 -L);
19259 +                capcp     = capc / 100;      capCat = put(capcp, PERCENT6.0 -L);
19260 +                decilep   = decile / 100;    decCat = put(decilep, PERCENT6.0 -L);
19261 +                liftCat = lift;
19262 +                liftCCat= liftC;
19263 +                if eof then call symput("_tarexists_", strip(target));
19264 +            run;
19266 +            /* create template for Captured Response Plot */
19268 +            ods path(prepend) work.templat(update);
19269 +            proc template;
19270 +                define statgraph reg_emrank / store=work.templat;
19271 +                    begingraph / designheight=470px;
19272 +                    %if "&EM_REPORT_TITLE" ne ""
19273 +                        %then %do; entrytitle "&EM_REPORT_TITLE"; %end;
19274 +                        %else %do; entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))"; %end;
19275 +                        entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_rptmodelgains_title, NOQUOTE))";
19276 +                        layout overlay / cycleattrs=true xaxisopts=(display=(LINE)) yaxisopts=(label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptcumcaptresponse_vlabel, NOQUOTE))");
19277 +                            seriesplot x=deccat y=basecapcp / lineattrs=(pattern=solid) name='base'  legendlabel="%sysfunc(sasmsg(sashelp.dmine, rpt_rptbase_vlabel, NOQUOTE))";
19278 +                            seriesplot x=deccat y=bestcapcp / lineattrs=(pattern=solid) name='best'  legendlabel="%sysfunc(sasmsg(sashelp.dmine, rpt_rptbest_vlabel, NOQUOTE))";
19279 +                            seriesplot x=deccat y=capcp     / lineattrs=(pattern=solid) name='model' legendlabel="%sysfunc(sasmsg(sashelp.dmine, rpt_rptmodel_vlabel, NOQUOTE))";
19280 +                            innermargin / align=bottom;
19281 +                                blockplot x=deccat block=liftCcat / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptcumlift_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19282 +                                                                    repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata5 altfillattrs=graphdata5;
19283 +                                blockplot x=deccat block=liftcat  / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptlift_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19284 +                                                                    repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata4 altfillattrs=graphdata4;
19285 +                                blockplot x=deccat block=capcat   / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptmodel_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19286 +                                                                    repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata3 altfillattrs=graphdata3;
19287 +                                blockplot x=deccat block=bestcat  / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptbest_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19288 +                                                                    repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata2 altfillattrs=graphdata2;
19289 +                                blockplot x=deccat block=deccat   / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptdecile_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19290 +                                                                    repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdatadefault altfillattrs=graphdatadefault;
19291 +                            endinnermargin;
19292 +                            * discretelegend "base" "best" "model" / across=1 location=inside halign=right valign=bottom;
19293 +                        endlayout;
19294 +                    endgraph;
19295 +                end;
19296 +            run;
19297 +        %end;
19298 +        %else %do;
19300 +            /* generate % values and create categorical x values for plotting purposes */
19301 +            data &EM_USER_CAPRESPONSE;
19302 +                set &em_report_rank end=eof;
19303 +                length decCat $5;
19304 +                format  decilep PERCENT6.0;
19305 +                decilep = decile / 100;
19306 +                decCat = put(decilep, PERCENT6.0 -L);
19307 +                if eof then call symput("_tarexists_", strip(target));
19308 +            run;
19310 +            /* create template for Captured Response Plot */
19311 +            ods path(prepend) work.templat(update);
19312 +            proc template;
19313 +                define statgraph reg_emrank / store=work.templat;
19314 +                    begingraph / designheight=470px ;
19315 +                    %if "&EM_REPORT_TITLE" ne ""
19316 +                        %then %do; entrytitle "&EM_REPORT_TITLE"; %end;
19317 +                        %else %do; entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))"; %end;
19318 +                        entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_rptmodelgainspred_title, NOQUOTE))";
19319 +                        layout overlay / cycleattrs=true xaxisopts=(display=(LINE)) yaxisopts=(label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptpredresp_vlabel, NOQUOTE))");
19320 +                            seriesplot x=deccat y=_meanp_ / lineattrs=(pattern=solid) name='mean' legendlabel="%sysfunc(sasmsg(sashelp.dmine, rpt_mean_vlabel, NOQUOTE))";
19321 +                            seriesplot x=deccat y=_minp_ / lineattrs=(pattern=solid) name='min' legendlabel="%sysfunc(sasmsg(sashelp.dmine, rpt_minimum_vlabel, NOQUOTE))";
19322 +                            seriesplot x=deccat y=_maxp_     / lineattrs=(pattern=solid) name='max' legendlabel="%sysfunc(sasmsg(sashelp.dmine, rpt_maximum_vlabel, NOQUOTE))";
19323 +                            innermargin / align=bottom;
19324 +                                blockplot x=deccat block=_targetmean_  / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rpttargetmean_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19325 +                                                                         repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata5 altfillattrs=graphdata5;
19326 +                                blockplot x=deccat block=_targetmin_   / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rpttargetmin_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19327 +                                                                         repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata4 altfillattrs=graphdata4;
19328 +                                blockplot x=deccat block=_targetmax_   / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rpttargetmax_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19329 +                                                                         repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata3 altfillattrs=graphdata3;
19330 +                                blockplot x=deccat block=_meanp_       / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptpredmean_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19331 +                                                                         repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata5 altfillattrs=graphdata5;
19332 +                                blockplot x=deccat block=_minp_        / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptpredmin_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19333 +                                                                         repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata4 altfillattrs=graphdata4;
19334 +                                blockplot x=deccat block=_maxp_        / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptpredmax_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19335 +                                                                         repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdata3 altfillattrs=graphdata3;
19336 +                                blockplot x=deccat block=deccat        / label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptdecile_vlabel, NOQUOTE))" valuehalign=center filltype=alternate
19337 +                                                                         repeatedvalues=true valueattrs=(size=7) VALUEFITPOLICY=SHRINK  fillattrs=graphdatadefault altfillattrs=graphdatadefault;
19338 +                            endinnermargin;
19339 +                            * discretelegend "mean" "min" "max" / across=1 location=inside halign=left valign=bottom;
19340 +                        endlayout;
19341 +                    endgraph;
19342 +                end;
19343 +            run;
19344 +        %end;
19346 +        /* render plot */
19348 +        %let validflag=0;
19349 +        data tempvalid;
19350 +            set &EM_USER_CAPRESPONSE(where=(datarole="VALIDATE"));
19351 +        run;
19353 +        %let dsid = %sysfunc(open(work.tempvalid));
19354 +        %if &dsid %then %do;
19355 +            %let validobs = %sysfunc(attrn(&dsid, NOBS));
19356 +            %if &validobs gt 0 %then %let validflag = 1;
19357 +        %end;
19358 +        %if &dsid %then %let dsid = %sysfunc(close(&dsid));
19360 +        proc sgrender data=&EM_USER_CAPRESPONSE
19361 +            %if "&_tarexists_" ne "." %then %do; (where=(target="&rpt_rpm_targetName"))%end;
19362 +            template=reg_emrank;
19363 +            %if &validflag eq 1
19364 +                %then %do; where datarole="VALIDATE"; %end;
19365 +                %else %do; where datarole="TRAIN";    %end;
19366 +        run;
19367 +    %end;
19368 +%end;
19371 +/* RPM-7. ROC CHART ******************************************************************************************/
19373 +%if ((&RPM_REPORT_MDLCOMP ne ) or (&em_report_modelid ne )) and (&EM_PROPERTY_BASICOUTPUT eq Y) %then %do;
19375 +    %let em_report_roc = &em_lib..&RPM_REPORT_MDLCOMP._emroc;
19376 +    %let em_report_hpassess = &em_lib..%sysfunc(trim(&em_report_modelid))_hpassess;
19377 +    %let em_report_hpstats  = &em_lib..%sysfunc(trim(&em_report_modelid))_hpreportstats;
19379 +    %EM_GETNAME(key=ROC, type=DATA);
19380 +    %let plot_KS  = 0;
19381 +    %let plot_ROC = 0;
19382 +    %let train_X  = 0; %let train_Y  = 0; %let train_KS = 0;
19383 +    %let valid_X  = 0; %let valid_Y  = 0; %let valid_KS = 0;
19385 +    %if %sysfunc(exist(&em_report_roc)) %then %do;
19387 +        proc sql noprint;
19388 +            select count(*) into :validroc from &em_report_roc where datarole eq "VALIDATE";
19389 +        quit;
19391 +        /* make the ROC table */
19392 +        data &EM_USER_ROC;
19393 +            length group $32 diff 8;
19394 +            label group="&em_report_modeldesc %sysfunc(sasmsg(sashelp.dmine, rpt_rptmodel_vlabel, NOQUOTE))";
19395 +            set &em_report_roc;
19396 +            if strip(model)='_Baseline_'
19397 +                then group='Baseline';
19398 +                else group=Datarole;
19399 +            where (strip(model) ='_Baseline_' and DataRole='TRAIN') or (strip(model)= "&em_report_modelid" and DataRole in('TRAIN','VALIDATE'));
19400 +            diff = sensitivity-oneminusspecificity;
19401 +        run;
19403 +        /* get KS statistics */
19405 +        proc sql noprint;
19406 +            create table ks_stat as
19407 +                select distinct datarole, max(diff) as ks from &EM_USER_ROC group by datarole;
19408 +            select round(ks, 0.00001) into :train_ks
19409 +                from ks_stat where datarole eq 'TRAIN';
19410 +            select round(ks, 0.00001) into :valid_ks
19411 +                from ks_stat where datarole eq 'VALIDATE';
19412 +            select oneMinusSpecificity, sensitivity into :train_x, :train_y
19413 +                from &EM_USER_ROC where datarole eq 'TRAIN' and round(diff, 0.00001) eq &train_ks;
19414 +            select oneMinusSpecificity, sensitivity into :valid_x, :valid_y
19415 +                from &EM_USER_ROC where datarole eq 'VALIDATE' and round(diff, 0.00001) eq &valid_ks;
19416 +        quit;
19418 +        %let plot_KS = 1;
19419 +        %let plot_ROC = 1;
19420 +    %end;
19421 +    %else %if %sysfunc(exist(&em_report_hpassess)) and "&rpt_rpm_targetLevel" ne "INTERVAL" %then %do;
19423 +        /* make the ROC table */
19425 +        proc sql noprint;
19426 +            select count(*) into :validroc from &em_report_hpassess where datarole eq "VALIDATE";
19427 +        quit;
19429 +        data &EM_USER_ROC;
19430 +            length group $32;
19431 +            group='Baseline';
19432 +            sensitivity = 0; oneminusspecificity = 0; output;
19433 +            sensitivity = 1; oneminusspecificity = 1; output;
19434 +        run;
19436 +        data &EM_USER_ROC;
19437 +            length group $32;
19438 +            set &em_report_hpassess(in=in1 rename=(one_minus_specificity=oneminusspecificity)) &EM_USER_ROC;
19439 +            if in1 then group=datarole;
19440 +            label group="&em_report_modeldesc %sysfunc(sasmsg(sashelp.dmine, rpt_rptmodel_vlabel, NOQUOTE))";
19441 +        run;
19443 +        /* get KS statistics */
19445 +        %if %sysfunc(exist(&em_report_hpstats)) %then %do;
19447 +             data _null_;
19448 +                set &em_report_hpstats;
19449 +                if upcase(stat) eq 'KSREF' then call symput('train_X', put(train,best24.));
19450 +                if upcase(stat) eq 'KSR'   then call symput('train_KS',round(train,0.00001));
19451 +                %if &validroc ge 1 %then %do;
19452 +                    if upcase(stat) eq 'KSREF' then call symput('valid_X', put(validate,best24.));
19453 +                    if upcase(stat) eq 'KSR'   then call symput('valid_KS',round(validate,0.00001));
19454 +                %end;
19455 +            run;
19457 +            data _null_;
19458 +                tmp=&train_X+&train_KS/100; call symput('train_Y',put(tmp,best24.));
19459 +                tmp=&valid_X+&valid_KS/100; call symput('valid_Y',put(tmp,best24.));
19460 +            run;
19462 +            %if &train_Y gt 0 %then %let plot_KS = 1;
19463 +            %let plot_ROC = 1;
19464 +        %end;
19465 +    %end;
19467 +    %if &plot_ROC eq 1 %then %do;
19469 +        /* add coordinates for ref lines */
19470 +        data refline;
19471 +            length oneminusspecificity sensitivity x y 8 datarole $8 group $32 text $60;
19472 +            %if &train_Y gt 0 %then %do;
19473 +                oneminusspecificity=&TRAIN_X; sensitivity=&TRAIN_Y; datarole='TRAIN'; group='TRAIN';
19474 +                text=""; x=.; y=.; output;
19475 +                oneminusspecificity=&TRAIN_X; sensitivity=&TRAIN_X; datarole='TRAIN'; group='TRAIN';
19476 +                text=catx(' = ',"%sysfunc(sasmsg(sashelp.dmine, rpt_trainks_vlabel, NOQUOTE))","&train_KS"); x=&TRAIN_X; y=&TRAIN_X - 0.05; output;
19477 +                oneminusspecificity=&TRAIN_X; sensitivity=&TRAIN_Y; datarole='TRAIN'; group='TRAIN';
19478 +                text=""; x=.; y=.; output;
19479 +            %end;
19480 +            %if &validroc ge 1 and &valid_Y gt 0 %then %do;
19481 +                oneminusspecificity=&VALID_X; sensitivity=&VALID_Y; datarole='VALIDATE'; group='VALIDATE';
19482 +                text=""; x=.; y=.; output;
19483 +                oneminusspecificity=&VALID_X; sensitivity=&VALID_X; datarole='VALIDATE'; group='VALIDATE';
19484 +                text=catx(' = ',"%sysfunc(sasmsg(sashelp.dmine, rpt_validks_vlabel, NOQUOTE))","&valid_KS"); x=&VALID_X; y=&VALID_X - 0.05; output;
19485 +                oneminusspecificity=&VALID_X; sensitivity=&VALID_Y; datarole='VALIDATE'; group='VALIDATE';
19486 +                text=""; x=.; y=.; output;
19487 +            %end;
19488 +        data &EM_USER_ROC;
19489 +           set &EM_USER_ROC refline;
19490 +           if group eq 'TRAIN'    then group = "%sysfunc(sasmsg(sashelp.dmine, rpt_train_vlabel, NOQUOTE))";
19491 +           if group eq 'VALIDATE' then group = "%sysfunc(sasmsg(sashelp.dmine, rpt_validate_vlabel, NOQUOTE))";
19492 +           if group eq 'Baseline' then group = "%sysfunc(sasmsg(sashelp.dmine, rpt_baselineroc_value, NOQUOTE))";
19493 +        run;
19495 +        /* plot ROC chart */
19496 +        ods path(prepend) work.templat(update);
19497 +        proc template;
19498 +            define statgraph rocplot / store=work.templat;
19499 +                begingraph / designheight=470px;
19500 +                    %if "&EM_REPORT_TITLE" ne ""
19501 +                        %then %do; entrytitle "&EM_REPORT_TITLE"; %end;
19502 +                        %else %do; entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_reporttitleone_title, NOQUOTE))"; %end;
19503 +                    entrytitle "%sysfunc(sasmsg(sashelp.dmine, rpt_rptroc_title, NOQUOTE))";
19504 +                    layout overlay;
19505 +                        seriesplot x=oneminusspecificity y=sensitivity / group =group lineattrs=(pattern=solid) name='series';
19506 +                        %if &plot_ks eq 1 %then %do;
19507 +                            scatterplot x=x y=y / group=group datalabel=text  datalabelattrs=(weight=bold) markerattrs=(size=0px);
19508 +                        %end;
19509 +                        discretelegend 'series' / title="&em_report_modeldesc  %sysfunc(sasmsg(sashelp.dmine, rpt_rptmodel_vlabel, NOQUOTE))";
19510 +                    endlayout;
19511 +                endgraph;
19512 +            end;
19513 +        run;
19515 +        proc sort data=&EM_USER_ROC; by group oneminusspecificity; run;
19516 +        proc sgrender data=&EM_USER_ROC template=rocplot; run;
19517 +        title1; title2;
19518 +    %end;
19519 +%end;
19521 +/* RPM-8. Scorecard *****************************************************************************************/
19523 +%if &EM_PROPERTY_BASICOUTPUT eq Y %then %do;
19525 +    %if %sysfunc(exist(&EM_USER_ScoreCard)) ne 0 and &runHP eq 0 %then %do;
19527 +        %let ColPercNum = 0;
19528 +        %let sdsid = %sysfunc(open(&EM_USER_SCORECARD));
19529 +        %let colPercnum = %sysfunc(varnum(&sdsid, colPercent));
19530 +        %if &sdsid %then %let sdsid = %sysfunc(close(&sdsid));
19532 +        %let tmpstr1 = %sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_scpoints, NOQUOTE));
19533 +        %let tmpstr2 = %sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_overall, NOQUOTE));
19534 +        %let tmpstr3 = %sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_singleN, NOQUOTE));
19536 +        %if &EM_PROPERTY_CROSSTABS eq Y and "&rpt_rpm_targetLevel" ne "INTERVAL" and &colPercnum gt 0
19537 +        %then %do;
19539 +            ods &EM_REPORT_FORMAT text=" ";
19540 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_crosstab_title, NOQUOTE))";
19541 +            ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_crosstab_title, NOQUOTE))";
19543 +            proc tabulate data=&EM_USER_SCORECARD order=data noseps;
19544 +                class displayvar rowVariable varvalue &rpt_rpm_targetName / missing;
19545 +                var allfreq allPercent scorepoints colpercent frequency;
19546 +                keylabel sum=" ";
19547 +                table displayvar*varvalue,
19548 +                    (scorepoints="&tmpstr1" allfreq="&tmpstr2 &tmpstr3" allpercent="&tmpstr2 %" )
19549 +                    &rpt_rpm_targetName*(frequency="&tmpstr3" colPercent="%" )
19550 +                    / indent=3 rtspace=32 style=[outputwidth=100%];
19551 +            run; quit;
19552 +        %end;
19553 +        %else %do;
19555 +            ods &EM_REPORT_FORMAT text=" ";
19556 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptrpmscorecard_title, NOQUOTE))";
19557 +            ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptrmpscorecard_title, NOQUOTE))";
19559 +            proc tabulate data = &EM_USER_SCORECARD order=data noseps;
19560 +                class displayvar / missing;
19561 +                keylabel sum=" ";
19562 +                class varvalue / missing;
19563 +                var scorepoints;
19564 +                table displayvar*varvalue, (scorepoints="&tmpstr1")*sum
19565 +                    / misstext = " " indent = 3 rtspace=32;
19566 +            run; quit;
19567 +         %end;
19568 +    %end;
19571 +    %if %sysfunc(exist(&EM_USER_ScoreCard)) ne 0 and &runHP eq 1 %then %do;
19573 +        %if &EM_PROPERTY_CROSSTABS eq Y and "&rpt_rpm_targetLevel" ne "INTERVAL" %then %do;
19574 +            ods &EM_REPORT_FORMAT text=" ";
19575 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_crosstab_title, NOQUOTE))";
19576 +            ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_crosstab_title, NOQUOTE))";
19577 +            proc tabulate data=&EM_USER_SCORECARD order=data noseps;
19578 +                class displayvar rowVariable displayLvl &rpt_rpm_targetName / MISSING;
19579 +                var allfreq allPercent scorepoints colpercent frequency;
19580 +                keylabel sum=" ";
19581 +                table displayvar * displayLvl, (scorepoints="%sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_scpoints, NOQUOTE))" allfreq="%sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_overall, NOQUOTE)) N"
19581!+allpercent="%sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_overall, NOQUOTE)) %" )
19582 +                    &rpt_rpm_targetName*(frequency="N" colPercent="%" )
19583 +                    / indent=3 rtspace=32 style=[outputwidth=100%];
19584 +            run; quit;
19585 +        %end;
19586 +        %else %do;
19587 +            ods &EM_REPORT_FORMAT text=" ";
19588 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptrpmscorecard_title, NOQUOTE))";
19589 +            ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptrpmscorecard_title, NOQUOTE))";
19590 +            proc tabulate data = &EM_USER_SCORECARD order=data noseps;
19591 +                class displayvar / MISSING;
19592 +                keylabel sum=" ";
19593 +                class displayLvl / missing;
19594 +                var SCOREPOINTS;
19595 +                table displayvar * displayLvl, (scorepoints="%sysfunc(sasmsg(sashelp.dmine, rpt_rptscorecard_scpoints, NOQUOTE))")  * sum / misstext = " "
19596 +                      indent = 3 rtspace=32 style=[outputwidth=50%];
19597 +            run; quit;
19598 +        %end;
19599 +    %end;
19600 +%end;
19602 +%if &RPM_REPORT_SCORENODE ne %then %do;
19604 +    /* RPM-9. Classification matrix *********************************************************************/
19606 +    %if &EM_PROPERTY_CLASSIFICATION eq Y %then %do;
19608 +        %let em_report_class = &em_lib..%sysfunc(trim(&em_report_modelid))_emclassification;
19610 +        %if %sysfunc(exist(&em_report_class)) %then %do;
19612 +            %EM_GETNAME(key=MISCLASS, type=DATA);
19613 +            data &EM_USER_MISCLASS;
19614 +                set &em_report_class;
19615 +                where _TYPE_="PREDICTION" AND TARGET="&rpt_rpm_targetName";
19616 +            run;
19618 +            %let numobs=0;
19619 +            %let dsid = %sysfunc(open(&EM_USER_MISCLASS));
19620 +            %let numobs = %sysfunc(attrn(&dsid, NOBS));
19621 +            %if &dsid %then %let dsid = %sysfunc(close(&dsid));
19623 +            %if (%sysfunc(exist(&em_USER_MISCLASS)) and &numobs gt 0) %then %do;
19625 +                ods &EM_REPORT_FORMAT text=" ";
19626 +                %if "%nrbquote(&rpt_rpm_targetLabel)" ne "" %then %do;
19627 +                    ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptclassmatrix_title, NOQUOTE, %nrstr(&rpt_rpm_targetLabel)))";
19628 +                    ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptclassmatrix_title, NOQUOTE, %nrstr(&rpt_rpm_targetLabel)))";
19629 +                %end;
19630 +                %else %do;
19631 +                    ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptclassmatrix_title, NOQUOTE, &rpt_rpm_targetName))";
19632 +                    ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptclassmatrix_title, NOQUOTE, &rpt_rpm_targetName))";
19633 +                %end;
19635 +                proc tabulate data=&EM_USER_MISCLASS order=data noseps;
19636 +                    keylabel sum=" ";
19637 +                    class from into datarole;
19638 +                    var pct_col;
19639 +                    table from , (datarole*into='Predicted') * pct_col="" / style=[outputwidth=50%];
19640 +                run; quit;
19641 +            %end;
19642 +        %end;
19643 +    %end;
19645 +    /* RPM-10. Fit Statistics **************************************************************************/
19647 +    %if &EM_PROPERTY_FITSTAT eq Y %then %do;
19649 +        %let fitdat1 =&em_lib..&RPM_REPORT_MDLCOMP._emreportfit;
19650 +        %let fitdat2 =&em_lib..&em_report_modelid._emreportfit;
19652 +        /* prefer to use HPREPORTSTATS table from the modeling nodel */
19654 +        %if %sysfunc(exist(&em_lib..&em_report_modelid._hpreportstats)) %then %do;
19656 +            %EM_GETNAME(key=REPORTFIT, type=DATA);
19658 +            data &EM_USER_REPORTFIT;
19659 +                set &em_lib..&em_report_modelid._hpreportstats;
19660 +            run;
19662 +            %let validfit=0;
19663 +            %let dsid = %sysfunc(open(&EM_USER_REPORTFIT));
19664 +            %if &dsid %then %do;
19665 +                %let validnum = %sysfunc(VARNUM(&dsid, VALIDATE));
19666 +                %if &validnum %then %let validfit = 1;
19667 +            %end;
19668 +            %if &dsid %then %let dsid = %sysfunc(close(&dsid));
19670 +            ods &EM_REPORT_FORMAT text=" ";
19671 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_modelfitstat_title, NOQUOTE))";
19672 +            ods proclabel = "%sysfunc(sasmsg(sashelp.dmine, rpt_modelfitstat_title, NOQUOTE))";
19674 +            proc print data=&EM_USER_REPORTFIT label noobs style=[outputwidth=50%];
19675 +                %if &validfit eq 1 %then %do;
19676 +                    var Label Train Validate;
19677 +                    format Train 12.4 Validate 12.4;
19678 +                    label Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptstatistic_vlabel, NOQUOTE))"
19679 +                        train = "%sysfunc(sasmsg(sashelp.dmine, rpt_train_vlabel , NOQUOTE))"
19680 +                        validate = "%sysfunc(sasmsg(sashelp.dmine, rpt_validate_vlabel , NOQUOTE))";
19681 +                %end;
19682 +                %else %do;
19683 +                    var Label Train;
19684 +                    label Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptstatistic_vlabel, NOQUOTE))"
19685 +                        train = "%sysfunc(sasmsg(sashelp.dmine, rpt_train_vlabel , NOQUOTE))";
19686 +                    format Train 12.4;
19687 +                %end;
19688 +            run;
19689 +        %end;
19690 +        %else %if %sysfunc(exist(&fitdat1)) or %sysfunc(exist(&fitdat2)) %then %do;
19692 +            %EM_GETNAME(key=REPORTFIT, type=DATA);
19694 +            data &EM_USER_REPORTFIT;
19695 +                length Label $40;
19696 +                label Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptstatistic_vlabel, NOQUOTE))";
19697 +                %if %sysfunc(exist(&fitdat1)) eq 1
19698 +                    %then %do; set &fitdat1; %end;
19699 +                    %else %do; set &fitdat2.(rename=(stat=fitstat)); %end;
19701 +                if FitStat='_LIFT_'  then Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptlift10_vlabel, NOQUOTE))";
19702 +                if FitStat='_LIFTC_' then Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptcumlift10_vlabel, NOQUOTE))";
19703 +                if FitStat='_CAP_'  then Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptcaptresp10_vlabel, NOQUOTE))";
19704 +                if FitSTat='_CAPC_' then Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptcumcaptresp10_vlabel, NOQUOTE))";
19705 +                if FitSTat='_NOBS_' then Label="%sysfunc(sasmsg(sashelp.dmine, stat_nobs_vlabel, NOQUOTE))";
19706 +                if FitSTat='_MISC_' then Label= "%sysfunc(sasmsg(sashelp.dmine, stat_misc_vlabel, NOQUOTE))";
19707 +                if FitSTat='_MAX_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, stat_max_vlabel, NOQUOTE))";
19708 +                if FitSTat='_SSE_'  then Label= "%sysfunc(sasmsg(sashelp.dmine, stat_sse_vlabel, NOQUOTE))";
19709 +                if FitSTat='_ASE_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_ase, NOQUOTE))";
19710 +                if FitSTat='_RASE_' then Label = "%sysfunc(sasmsg(sashelp.dmine, stat_rase_vlabel, NOQUOTE))";
19711 +                if FitSTat='_DIV_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, stat_div_vlabel, NOQUOTE))";
19712 +                if FitSTat='_DFT_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_dft, NOQUOTE))";
19713 +                if FitSTat='_AIC_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_aic, NOQUOTE))";
19714 +                if FitSTat='_MSE_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_mse, NOQUOTE))";
19715 +                if FitSTat='_RMSE_' then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_rmse, NOQUOTE))";
19716 +                if FitSTat='_AVERR_' then Label = "%sysfunc(sasmsg(sashelp.dmine, stat_averr_vlabel, NOQUOTE))";
19717 +                if FitSTat='_DFE_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_dfe, NOQUOTE))";
19718 +                if FitSTat='_DFM_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_dfm, NOQUOTE))";
19719 +                if FitSTat='_ERR_'  then Label= "%sysfunc(sasmsg(sashelp.dmine, stat_err_vlabel, NOQUOTE))";
19720 +                if FitSTat='_FPE_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_fpe, NOQUOTE))";
19721 +                if FitSTat='_NW_'   then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_nw, NOQUOTE))";
19722 +                if FitSTat='_RFPE_' then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_rfpe, NOQUOTE))";
19723 +                if FitSTat='_SBC_'  then Label = "%sysfunc(sasmsg(sashelp.dmine, rpt_fitstat_label_sbc, NOQUOTE))";
19724 +            run;
19726 +            %let validfit=0;
19727 +            %let dsid = %sysfunc(open(&EM_USER_REPORTFIT));
19728 +            %if &dsid %then %do;
19729 +                %let validnum = %sysfunc(VARNUM(&dsid, VALIDATE));
19730 +                %if &validnum %then %let validfit = 1;
19731 +            %end;
19732 +            %if &dsid %then %let dsid = %sysfunc(close(&dsid));
19734 +            ods &EM_REPORT_FORMAT text=" ";
19735 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_modelfitstat_title, NOQUOTE))";
19736 +            ods proclabel = "%sysfunc(sasmsg(sashelp.dmine, rpt_modelfitstat_title, NOQUOTE))";
19738 +            proc print data=
19739 +                %if %sysfunc(exist(&em_lib..&RPM_REPORT_MDLCOMP._emreportfit)) eq 1
19740 +                %then %do;
19741 +                    &EM_USER_REPORTFIT(where=(MODEL="&em_report_modelId" AND TARGET="&rpt_rpm_targetName"))
19742 +                %end;
19743 +                %else %do;
19744 +                    &EM_USER_REPORTFIT(where=(TARGET="&rpt_rpm_targetName"))
19745 +                %end;
19746 +                label noobs style=[outputwidth=50%];
19747 +                %if &validfit eq 1 %then %do;
19748 +                    var Label Train Validate;
19749 +                    format Train 12.4 Validate 12.4;
19750 +                    label Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptstatistic_vlabel, NOQUOTE))"
19751 +                        train = "%sysfunc(sasmsg(sashelp.dmine, rpt_train_vlabel , NOQUOTE))"
19752 +                        validate = "%sysfunc(sasmsg(sashelp.dmine, rpt_validate_vlabel , NOQUOTE))"
19753 +                    ;
19754 +                %end;
19755 +                %else %do;
19756 +                    var Label Train;
19757 +                    format Train 12.4;
19758 +                    label Label="%sysfunc(sasmsg(sashelp.dmine, rpt_rptstatistic_vlabel, NOQUOTE))"
19759 +                        train = "%sysfunc(sasmsg(sashelp.dmine, rpt_train_vlabel , NOQUOTE))";
19760 +                %end;
19761 +                %if %sysfunc(exist(&em_lib..&RPM_REPORT_MDLCOMP._emreportfit)) eq 1
19762 +                %then %do;
19763 +                    where FitStat in ('_NOBS_', '_MISC_', '_LOSS_', '_ALOSS_', '_ASE_', '_AUR_', '_GINI_', 'KS',
19764 +                                      '_KS_PROB_CUTOFF', '_LIFT_', '_LIFTC_', '_CAP_', '_CAPC_', '_AIC_', '_AVERR_',
19765 +                                      '_MAX_','_MSE_', '_RASE_', '_RMSE_', '_SBC_', '_SSE_') and TRAIN ne .;
19766 +                %end;
19767 +                %else %do;
19768 +                    where FitStat in ('_NOBS_', '_MISC_', '_LOSS_', '_ALOSS_', '_ASE_', '_AIC_', '_AVERR_', '_MAX_',
19769 +                                      '_MSE_', '_RASE_', '_RMSE_', '_SBC_', '_SSE_') and TRAIN ne .;
19770 +                %end;
19771 +            run;
19772 +        %end;
19773 +    %end;
19775 +    /* RPM-11. Model Comparison Table */
19777 +    %if &EM_PROPERTY_COMPAREMDL eq Y and RPM_REPORT_MDLCOMP ne %then %do;
19779 +        %EM_GETNAME(KEY=COMPAREMDL, TYPE=DATA);
19781 +        %let em_report_mdlOutfit = &em_lib..&RPM_REPORT_MDLCOMP._emoutfit;
19783 +        %if %sysfunc(exist(&em_report_mdlOutfit)) %then %do;
19785 +            /* determine model selection criterion - to included in print */
19786 +            %em_modelprops(dgmid=&em_lib, modelid=&RPM_REPORT_MDLCOMP, output=%nrbquote(work.modelprop));
19788 +            %let selectcrit = ;
19789 +            data _null_;
19790 +                set work.modelprop(where=(NAME="ModelCriteria")) end=eof;
19791 +                if eof then call symput('selectcrit', VALUE);
19792 +            run;
19794 +            %let selectstat=;
19795 +            %let choice = USE="Y";
19796 +            %let dsid = %sysfunc(open(&em_report_mdlOutfit(where=(&choice))));
19797 +            %if &dsid %then %do;
19798 +                %let obs = %sysfunc(fetchobs(&dsid, 1));
19799 +                %let selectstat = %sysfunc(VARNAME(&dsid, 6));
19800 +            %end;
19801 +            %if &dsid %then %let dsid = %sysfunc(close(&dsid));
19803 +            %let validflag=0;
19804 +            %let dsid = %sysfunc(open(&em_lib..&RPM_REPORT_MDLCOMP._emreportfit));
19805 +            %if &dsid %then %do;
19806 +                %let validnum = %sysfunc(VARNUM(&dsid, VALIDATE));
19807 +                %if &validnum %then %let validflag = 1;
19808 +            %end;
19809 +            %if &dsid %then %let dsid = %sysfunc(close(&dsid));
19811 +            data &EM_USER_COMPAREMDL;
19812 +                set &em_report_mdlOutfit;
19813 +                keep use model modelDescription &selectstat
19814 +                %if "&rpt_rpm_targetLevel" ne "INTERVAL" %then %do;
19815 +                    %if &selectstat ne _LIFT_ %then %do; _LIFT_ %end;
19816 +                    %if &validflag eq 1 %then %do;
19817 +                        %if &selectstat ne _VLIFT_ %then %do; _VLIFT_ %end;
19818 +                    %end;
19819 +                    %if &selectstat ne _AIC_ %then %do; _AIC_  %end;
19820 +                %end;
19821 +                %else %do;
19822 +                    %if &selectstat ne _ASE_ %then %do; _ASE_ %end;
19823 +                    %if &validflag eq 1 %then %do;
19824 +                        %if &selectstat ne _VASE_ %then %do; _VASE_ %end;
19825 +                    %end;
19826 +                %end;
19827 +                ;
19828 +            run;
19830 +            ods &EM_REPORT_FORMAT text=" ";
19831 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptcomparemdl_title, NOQUOTE, &selectcrit))";
19832 +            ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptcomparemdl_title, NOQUOTE, &selectcrit))";
19834 +            proc print data=&EM_USER_COMPAREMDL label noobs style=[outputwidth=50%]; run;
19835 +        %end;
19836 +    %end;
19838 +    /* RPM-12. Project Info */
19840 +    %if &EM_PROPERTY_BASICOUTPUT eq Y %then %do;
19842 +        %let pos = %index(&em_nodedir, Workspaces);
19843 +        %if &pos %then %do;
19845 +            %let projpath = %nrbquote(%sysfunc(trim(%sysfunc(substr(&em_nodedir, 1, %eval(&pos-2))))));
19846 +            %let reverse = %nrbquote(%sysfunc(reverse(&projpath)));
19847 +            %let pos2 = %index(&reverse, &em_dsep);
19848 +            %let len = %length(&projpath);
19849 +            %let projname = %nrbquote(%substr(&projpath, %eval(2+ &len-&pos2)));
19850 +            %let projname =%nrbquote(%sysfunc(tranwrd(%nrstr(&projname), %str(%"),"")));
19851 +            %let wsname  = %nrbquote(%sysfunc(tranwrd(%nrstr(&em_wsname), %str(%"),"")));
19853 +            %EM_GETNAME(key=PROJINFO, type=DATA);
19854 +            data &EM_USER_PROJINFO;
19855 +                length stat $200 value $200;
19856 +                stat="%sysfunc(sasmsg(sashelp.dmine, rpt_varsel_label_name, NOQUOTE))";
19857 +                value="&projname";
19858 +                output;
19860 +                stat="%sysfunc(sasmsg(sashelp.dmine, views_descriptions_diagram, NOQUOTE))";
19861 +                value="&wsname";
19862 +                output;
19864 +                stat="%sysfunc(sasmsg(sashelp.dmine, rpt_rptpath_title, NOQUOTE))";
19865 +                value="&projpath";
19866 +                output;
19868 +                %let createTime = %sysfunc(left(%sysfunc(putn(%sysfunc(dateTime()), NLDATM24.0))));
19869 +                stat="%sysfunc(sasmsg(sashelp.dmine, rpt_crdate_vlabel, NOQUOTE))";
19870 +                value="&createTime";
19871 +                output;
19873 +                label stat="%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))"
19874 +                      value="%sysfunc(sasmsg(sashelp.dmine, rpt_rptvalue_vlabel, NOQUOTE))";
19876 +            run;
19878 +            ods &EM_REPORT_FORMAT text=" ";
19879 +            ods &EM_REPORT_FORMAT text="%sysfunc(sasmsg(sashelp.dmine, rpt_rptprojinfo_title, NOQUOTE))";
19880 +            ods proclabel ="%sysfunc(sasmsg(sashelp.dmine, rpt_rptprojinfo_title, NOQUOTE))";
19882 +            proc print data=&EM_USER_PROJINFO noobs label style=[outputwidth=50%]; run;
19883 +        %end;
19884 +    %end;
19885 +%end;
19887 +%skip:
19889 +%mend em_report_summary;
19892 +%macro em_reporter_scorecard(
19893 +/**----------------------------------------------------------------------**/
19894 +/*  EM_REPORTER_SCORECARD                                                 */
19895 +/*    generate scorecard report for EM RPM flows. currently this is an    */
19896 +/*    interval macro for RPM and does not perform any argument checking.  */
19897 +/**----------------------------------------------------------------------**/
19898 +inData           = ,     /* input training data set                       */
19899 +inData2          = ,
19900 +inVariableSet    = ,     /* input variable metadata, must have the three  */
19901 +                         /* columns: level, role and &name                */
19902 +name             = name, /* variable name column in the metadata          */
19903 +libname          = work, /* libname to save the temporary binned data     */
19904 +crosstab         = Y,    /* create cross-tabulation or not. Default = Y   */
19905 +targetVar        = ,     /* target variable for regressions and scores    */
19906 +targetVar2       = ,     /* target variable for cross-tabulations         */
19907 +targetLevel      = ,     /* target level                                  */
19908 +freqVar          = ,     /* frequency variable                            */
19909 +outVarRank       = ,     /* output dataset for variable ranking (HPREDUCE)*/
19910 +outScorecard     =       /* output dataset for scorecard report           */
19911 +/**----------------------------------------------------------------------**/
19912 +/* Created: 08/20/2012, XXM                                               */
19913 +/**----------------------------------------------------------------------**/
19914 +);
19916 +/**--------------------------------------------------------------------------------------**/
19917 +/** 0. Configurations                                                                    **/
19918 +/**--------------------------------------------------------------------------------------**/
19920 +** get target level;
19921 +%let targetVar   = &targetVar;
19922 +%let targetVar2  = &targetVar2;
19923 +%let targetLevel = &targetLevel;
19925 +** check valid target name and level;    /* TBA */
19927 +** check VariableSet has LEVEL and NAME; /* TBA */
19929 +/**--------------------------------------------------------------------------------------**/
19930 +/** 1. Get the input variables from variable set                                         **/
19931 +/**--------------------------------------------------------------------------------------**/
19933 +%let nom_vars = ;
19934 +%let ord_vars = ;
19935 +%let int_vars = ;
19936 +%let n_nomvars = 0;
19937 +%let n_ordvars = 0;
19938 +%let n_intvars = 0;
19940 +data _null_;
19941 +    set &inVariableSet(where=(LEVEL in ('NOMINAL', 'BINARY') and upcase(&name) ne "&targetVar")) end=eof;
19942 +    call symput('nom_vars'!!strip(put(_N_,BEST.)), strip(&name));
19943 +    if eof then call symput('n_nomvars', strip(put(_N_,BEST.)));
19944 +data _null_;
19945 +    set &inVariableSet(where=(LEVEL='ORDINAL' and upcase(&name) ne "&targetVar")) end=eof;
19946 +    call symput('ord_vars'!!strip(put(_N_,BEST.)), strip(&name));
19947 +    if eof then call symput('n_ordvars', strip(put(_N_,BEST.)));
19948 +data _null_;
19949 +    set &inVariableSet(where=(LEVEL='INTERVAL' and upcase(&name) ne "&targetVar")) end=eof;
19950 +    call symput('int_vars'!!strip(put(_N_,BEST.)), strip(&name));
19951 +    if eof then call symput('n_intvars', strip(put(_N_,BEST.)));
19952 +run;
19954 +%let n_svars = %eval(&n_nomvars+&n_ordvars+&n_intvars);
19956 +%if ((&n_nomvars gt 0) OR (&n_ordvars gt 0) OR (&n_intvars gt 0)) %then %do;
19958 +    /**----------------------------------------------------------------------------------**/
19959 +    /** 2. PROC ARBOR                                                                    **/
19960 +    /**----------------------------------------------------------------------------------**/
19962 +    proc arbor data=&indata
19963 +        alpha=1 leafsize=5 mincatsize=5 maxbranch=8 maxdepth=1 NORULELIMIT
19964 +        maxrules=&n_svars maxsurrs=0 missing=USEINSEARCH exhaustive=5000
19965 +    ;
19966 +        %if &n_nomvars gt 0 %then %do;
19967 +            input %do i=1 %to &n_nomvars; &&nom_vars&i %end; / level = nominal;
19968 +        %end;
19969 +        %if &n_ordvars gt 0 %then %do;
19970 +            input %do i=1 %to &n_ordvars; &&ord_vars&i %end; / level = ordinal;
19971 +        %end;
19972 +        %if &n_intvars gt 0 %then %do;
19973 +            input %do i=1 %to &n_intvars; &&int_vars&i %end; / level = interval;
19974 +        %end;
19975 +        %if &freqvar ne %then %do; freq &freqvar; %end;
19976 +        target &targetVar / level=&targetLevel;
19977 +        save importance=importance rules=rules;
19978 +    run;
19980 +    /**----------------------------------------------------------------------------------**/
19981 +    /** 2. get VarRank output table                                                      **/
19982 +    /**----------------------------------------------------------------------------------**/
19984 +    /* keep only the top 50 input variables - based on rank */
19986 +    data &outVarRank(where=(rank le 50));
19987 +        length variable $32 worth 8;
19988 +        set rules;
19989 +        by rank;
19990 +        retain variable worth;
19991 +        label variable = "%sysfunc(sasmsg(sashelp.dmine, rpt_variable_vlabel, NOQUOTE))";
19992 +        keep variable rank worth;
19994 +        if STAT="VARIABLE" then variable=Character_value;
19995 +        if STAT="WORTH" then worth=Numeric_value;
19996 +        if last.rank then output;
19997 +    run;
19999 +    data &outVarRank;
20000 +        set &outVarRank;
20001 +        if worth=. then do;
20002 +            worth = 0.00;
20003 +            rank  = _N_;
20004 +        end;
20005 +    run;
20007 +    /* selected interval and class input variables */
20008 +    proc sql noprint;
20009 +        create table tempclass(drop=&name) as
20010 +            select a.*, b.*  from &outVarRank as a left join &inVariableSet as b
20011 +                on upcase(a.variable) eq upcase(b.&name)
20012 +            where level ne "INTERVAL"
20013 +            order by a.variable
20014 +        ;
20015 +        create table tempinterval(drop=&name) as
20016 +            select a.*, b.*  from &outVarRank as a left join &inVariableSet as b
20017 +                on upcase(a.variable) eq upcase(b.&name)
20018 +            where level eq "INTERVAL"
20019 +            order by a.variable
20020 +        ;
20021 +    quit;
20023 +    /* create array of class variables names */
20024 +    %let class_vars=;
20025 +    %let n_classvars=0;
20026 +    data _null_;
20027 +        set tempclass end=eof;
20028 +        call symput('class_vars'!!strip(put(_N_,BEST.)), strip(variable));
20029 +        if eof then call symput('n_classvars', strip(put(_N_,BEST.)));
20030 +    run;
20032 +    /**----------------------------------------------------------------------------------**/
20033 +    /** 3. Get Bining information                                                        **/
20034 +    /**----------------------------------------------------------------------------------**/
20036 +    %let dsid = %sysfunc(open(work.rules));
20037 +    %let numobs = %sysfunc(attrn(&dsid, NOBS));
20038 +    %let numvars = %sysfunc(attrn(&dsid, NVARS));
20039 +    %if &dsid %then %let dsid = %sysfunc(close(&dsid));
20041 +    %if %sysfunc(exist(&outVarRank)) and (&numobs gt 0) and (&numvars gt 0) %then %do;
20043 +        data rules;
20044 +            set rules;
20045 +            where STAT in ('VARIABLE', 'INTERVAL', 'MISSING');
20046 +            numeric_value = round(numeric_value, 0.001);
20047 +        proc sort data=rules;
20048 +            by rank numeric_value;
20049 +        run;
20051 +        data binning_rules;
20052 +            length origVar $32;
20053 +            set rules;
20054 +            by rank;
20055 +            retain origVar oldLB;
20056 +            keep origVar LB UB;
20058 +            if STAT="VARIABLE" then origVar = Character_value;
20059 +            if first.rank then oldLB=.;
20060 +            if STAT="INTERVAL" then do;
20061 +                LB = oldLB;
20062 +                UB = Numeric_value;
20063 +                oldLB=UB;
20064 +                output;
20065 +            end;
20066 +            if last.rank then do;
20067 +                LB=oldLB;
20068 +                UB = .;
20069 +                output;
20070 +            end;
20071 +        run;
20073 +        /* names for binning */
20074 +        proc sql noprint;
20075 +            create table tempbinnames(drop=variable) as
20076 +                select distinct origVar, role from binning_rules as a inner join tempinterval as b
20077 +                  on a.OrigVar eq b.variable;
20078 +        quit;
20080 +        /* get name server */
20081 +        proc dmdb data=tempbinnames outtable=tempbinnames nameserver;
20082 +            names origVar;
20083 +            prefix BIN_;
20084 +        run;
20086 +        /* merge binned names back to temp */
20087 +        proc sql noprint;
20088 +            create table binning_rules_names as
20089 +                select a.*, b.bin as binVar from binning_rules as a right join tempbinnames as b
20090 +                    on a.origVar eq b.origVar
20091 +                order by a.origVar, a.LB, a.UB;
20092 +        quit;
20094 +        /* create array of bin variables names */
20095 +        %let bin_vars=;
20096 +        %let n_binvars=0;
20097 +        data _null_;
20098 +            set tempbinnames end=eof;
20099 +            call symput('bin_vars'!!strip(put(_N_,BEST.)), strip(bin));
20100 +            if eof then call symput('n_binvars', strip(put(_N_,BEST.)));
20101 +        run;
20103 +    /**----------------------------------------------------------------------------------**/
20104 +    /** 4. Bin the interval variables                                                    **/
20105 +    /**    note that binned is used for DMREG, binned_full is used for CROSSTAB          **/
20106 +    /**----------------------------------------------------------------------------------**/
20108 +    data binned;
20109 +        set &indata;
20111 +        %if &n_binvars gt 0 %then %do;
20112 +            length %do i=1 %to &n_binvars; &&bin_vars&i %end; $50;
20113 +        %end;
20115 +        %let dsid = %sysfunc(open(binning_rules_names));
20116 +        %if &dsid %then %do;
20117 +            %let orignum = %sysfunc(VARNUM(&dsid, origVar));
20118 +            %let binnum  = %sysfunc(VARNUM(&dsid, binVar));
20119 +            %let lbnum   = %sysfunc(VARNUM(&dsid, LB));
20120 +            %let ubnum   = %sysfunc(VARNUM(&dsid, UB));
20122 +            %let obs     = %sysfunc(fetch(&dsid));
20123 +            %do %while(&obs=0);
20124 +                %let origvar = %sysfunc(getvarc(&dsid, &orignum));
20125 +                %let binvar  = %sysfunc(getvarc(&dsid, &binnum));
20126 +                %let lb   = %sysfunc(getvarn(&dsid, &lbnum));
20127 +                %let ub   = %sysfunc(getvarn(&dsid, &ubnum));
20129 +                %if "&lb" eq "." AND "&ub" ne "." %then %do;
20130 +                    %let count = 1;
20131 +                    if &origvar < &ub then &binvar = "&count: low - &ub";
20132 +                %end;
20133 +                %else %if "&lb" ne "." AND "&ub" eq "." %then %do;
20134 +                    %let count = %eval(&count+1);
20135 +                    else if &origvar >= &lb then &binvar = "&count: &lb - high";
20136 +                %end;
20137 +                %else %if "&lb" eq "." AND "&ub" eq "." %then %do;
20138 +                    %let count = 1;
20139 +                    &binvar = "&count: low - high";
20140 +                %end;
20141 +                %else %do;
20142 +                    %let count = %eval(&count+1);
20143 +                    else if &origvar < &ub AND &origvar >= &lb then &binvar = "&count: &lb - &ub";
20144 +                %end;
20146 +                %let obs = %sysfunc(fetch(&dsid));
20147 +            %end;
20148 +        %end;
20149 +        %if &dsid %then %let dsid = %sysfunc(close(&dsid));
20150 +    run;
20152 +    data binned_full;
20153 +        set &indata2;
20155 +        %if &n_binvars gt 0 %then %do;
20156 +            length %do i=1 %to &n_binvars; &&bin_vars&i %end; $50;
20157 +        %end;
20159 +        %let dsid = %sysfunc(open(binning_rules_names));
20160 +        %if &dsid %then %do;
20161 +            %let orignum = %sysfunc(VARNUM(&dsid, origVar));
20162 +            %let binnum  = %sysfunc(VARNUM(&dsid, binVar));
20163 +            %let lbnum   = %sysfunc(VARNUM(&dsid, LB));
20164 +            %let ubnum   = %sysfunc(VARNUM(&dsid, UB));
20166 +            %let obs     = %sysfunc(fetch(&dsid));
20167 +            %do %while(&obs=0);
20168 +                %let origvar = %sysfunc(getvarc(&dsid, &orignum));
20169 +                %let binvar  = %sysfunc(getvarc(&dsid, &binnum));
20170 +                %let lb   = %sysfunc(getvarn(&dsid, &lbnum));
20171 +                %let ub   = %sysfunc(getvarn(&dsid, &ubnum));
20173 +                %if "&lb" eq "." AND "&ub" ne "." %then %do;
20174 +                    %let count = 1;
20175 +                    if &origvar < &ub then &binvar = "&count: low - &ub";
20176 +                %end;
20177 +                %else %if "&lb" ne "." AND "&ub" eq "." %then %do;
20178 +                    %let count = %eval(&count+1);
20179 +                    else if &origvar >= &lb then &binvar = "&count: &lb - high";
20180 +                %end;
20181 +                %else %if "&lb" eq "." AND "&ub" eq "." %then %do;
20182 +                    %let count = 1;
20183 +                    &binvar = "&count: low - high";
20184 +                %end;
20185 +                %else %do;
20186 +                    %let count = %eval(&count+1);
20187 +                    else if &origvar < &ub AND &origvar >= &lb then &binvar = "&count: &lb - &ub";
20188 +                %end;
20190 +                %let obs     = %sysfunc(fetch(&dsid));
20191 +            %end;
20192 +        %end;
20193 +        %if &dsid %then %let dsid = %sysfunc(close(&dsid));
20194 +    run;
20196 +    /**----------------------------------------------------------------------------------**/
20197 +    /** 5. Generate scores                                                               **/
20198 +    /**----------------------------------------------------------------------------------**/
20200 +    proc dmdb batch data=binned dmdbcat=score_dmdb maxlevel=513 classout=_classout;
20201 +        class
20202 +            %if &n_classvars gt 0 %then %do; %do i=1 %to &n_classvars; &&class_vars&i %end; %end;
20203 +            %if &n_binvars gt 0   %then %do; %do i=1 %to &n_binvars;   &&bin_vars&i   %end; %end;
20204 +            %if "&targetLevel" ne "INTERVAL" %then %do; &targetVar %end;
20205 +        ;
20206 +        %if &freqvar ne %then %do; freq &freqvar; %end;
20207 +        %if "&targetLevel" eq  "INTERVAL" %then %do; var &targetVar; %end;
20208 +        target &targetVar;
20209 +    run; quit;
20211 +    proc dmreg data=binned dmdbcat=score_dmdb outest=coeff_est outterms=outterms noprint descending;
20212 +        class
20213 +            %if &n_classvars gt 0 %then %do; %do i=1 %to &n_classvars; &&class_vars&i %end; %end;
20214 +            %if &n_binvars gt 0   %then %do; %do i=1 %to &n_binvars;   &&bin_vars&i   %end; %end;
20215 +            %if "&targetLevel" ne "INTERVAL" %then %do; &targetVar %end;
20216 +        ;
20217 +        %if &freqvar ne %then %do; freq &freqvar; %end;
20218 +        model &targetVar =
20219 +            %if &n_classvars gt 0 %then %do; %do i=1 %to &n_classvars; &&class_vars&i %end; %end;
20220 +            %if &n_binvars gt 0   %then %do; %do i=1 %to &n_binvars;   &&bin_vars&i   %end; %end;
20221 +            / coding=glm
20222 +        ;
20223 +    run;
20227 +    proc sql noprint;
20228 +        create table _score_tmp1 as
20229 +            select *, min(coefficient) as min_est,
20230 +                case when calculated min_est = coefficient then 0
20231 +                     else coefficient-calculated min_est
20232 +                end as est1
20233 +            from outterms
20234 +            where variable ne 'Intercept'
20235 +            group by variable
20236 +        ;
20237 +        create table _score_tmp2 as
20238 +            select *, max(est1) as max_est1, count(*) as counter
20239 +            from _score_tmp1
20240 +            group by variable
20241 +        ;
20242 +%if "&fimnode" eq "" %then %do;
20243 +        create table _classout2(rename=(name=variable level=value)) as
20244 +            select a.*, b.format
20245 +                from _classout as a left join &inVariableSet(keep=&name format) as b
20246 +                on a.name eq b.&name
20247 +            order by a.name, a.level
20248 +        ;
20249 +%end;
20251 +        create table &outScorecard(rename=(classlevel=value)) as
20252 +            select *, sum(max_est1/counter) as sum_max,
20253 +                case when est1=max_est1 then 1 else 0 end as max_cat,
20254 +                round(1000*((est1) / calculated sum_max)) as score
20255 +            from _score_tmp2
20256 +            order by variable, classlevel
20257 +        ;
20259 +    quit;
20261 +    data &outScorecard;
20262 +%if "&fimnode" ne "" %then %do;
20263 +        set &outScorecard;
20264 +        by variable value;
20265 +%end;
20266 +%else %do;
20267 +        merge &outScorecard(in=_a) _classout2;
20268 +        by variable value;
20269 +        if _a;
20270 +%end;
20271 +        value = tranwrd(value, "'", "''");
20272 +        rename frequency   = AllFreq
20273 +               freqpercent = AllPercent;
20274 +        label  score       = "%sysfunc(sasmsg(sashelp.dmine, rpt_scorecardpoints_vlabel, NOQUOTE))"
20275 +               frequency   = "%sysfunc(sasmsg(sashelp.dmine, rpt_numObs_vlabel, NOQUOTE))"
20276 +               freqpercent = "%sysfunc(sasmsg(sashelp.dmine, rpt_rptperctobs_vlabel, NOQUOTE))";
20277 +    run;
20279 +    /* replace binned named with original var name instead */
20281 +    proc sort data=tempbinnames; by bin; run;
20283 +    data &outScorecard;
20284 +        merge &outScorecard(in=_a) tempbinnames(rename=(bin=variable origvar=displayVar)) ;
20285 +        by variable;
20286 +        if _a;
20287 +        if displayVar="" then displayVar=variable;
20288 +    run;
20290 +    /**----------------------------------------------------------------------------------**/
20291 +    /** 6. Generate crosstabs                                                            **/
20292 +    /**----------------------------------------------------------------------------------**/
20294 +    %if "&targetLevel" ne "INTERVAL" %then %do;
20296 +        ods &EM_REPORT_FORMAT exclude all;
20297 +        ods listing;
20299 +        data _null_;
20300 +            set &outScorecard end=eof;
20301 +            by variable;
20302 +            if _n_=1          then call execute("proc freq data=work.binned_full order=formatted addnames;");
20303 +            if first.variable then call execute("table "||variable||" * &targetVar2 / missing;");
20304 +            if eof then do;
20305 +                %if &freqvar ne %then %do; call execute("weight &freqvar;"); %end;
20306 +                call execute("ods output crosstabfreqs=tempfreq;");
20307 +                call execute("run;");
20308 +            end;
20309 +        run;
20311 +        data _null_;
20312 +            set &outScorecard end=eof;
20313 +            by variable value;
20315 +            if _n_=1 then do;
20316 +                call execute("data crosstabs;");
20317 +                call execute("  length varvalue $200 displayVar $32;");
20318 +                call execute("  set tempfreq(where=(_type_='11'));");
20319 +            end;
20321 +            if type="N" AND format eq "" then do;
20322 +                call execute("if "||Variable||" = "||value||" then do;");
20323 +            end;
20324 +            else do;
20325 +                if format ne ""
20326 +                    then call execute("%DMNORMCP(put("||variable||","||format||"), _normvar);");
20327 +                    else call execute("%DMNORMCP("||variable||", _normvar);");
20328 +                call execute("if strip(_normvar) = '"||strip(value)||"' then do;");
20329 +            end;
20331 +            call execute("  varvalue='"||strip(value)||"';");
20332 +            call execute("  scorepoints="||score||";");
20333 +            call execute("  allfreq="||allfreq||";");
20334 +            call execute("  allpercent="||allpercent||";");
20335 +            call execute("  displayVar='"||displayVar||"';");
20336 +            call execute("end;");
20338 +            if eof then call execute("run;");
20339 +        run;
20341 +        ods &EM_REPORT_FORMAT select all;
20342 +        ods listing close;
20344 +        proc sort data=crosstabs; by rowVariable varvalue; run;
20346 +        data &outScoreCard;
20347 +            set crosstabs;
20348 +            by rowVariable varvalue;
20349 +            if missing(scorepoints) then delete;
20350 +            if ^first.varvalue then do;
20351 +                 scorepoints=.;
20352 +                 allfreq=.;
20353 +                 allpercent=.;
20354 +            end;
20355 +            keep displayvar rowVariable varValue &targetVar2 scorepoints frequency colPercent allfreq allpercent ;
20356 +        run;
20357 +    %end;
20358 +    %else %do;
20359 +        data &outScoreCard;
20360 +            set &outScoreCard;
20361 +            rename value=varvalue score=scorepoints;
20362 +        run;
20363 +    %end;
20364 +%end;
20366 +/*    proc datasets lib=work nolist;*/
20367 +/*    run; quit;*/
20368 +%end;
20370 +%mend em_reporter_scorecard;
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMUTIL.EM_GETTRAINPATH.SOURCE.
20371 +%macro EM_GETTRAINPATH(NODEID=, OUTPATH=);
20372 +   proc display c=sashelp.emcore.pathscorecode.scl;
20373 +   run;
20374 +%mend EM_GETTRAINPATH;
20375 +
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
NOTE: %INCLUDE (level 1) file TEMP is file SASHELP.EMUTIL.REPORTER_TAGSET_TEMPLATE.SOURCE.
20376 +Ods path(prepend) work.templat(update);
20377 +
20378 +/* emgraph.tpl */
20379 +proc template;
20380 +define tagset tagsets.gtl / store=work.templat;
20381 +parent=tagsets.odsgraph;
20382 +%let  map =<>%nrstr(&)%str(%')%str(%");
20383 +map="&map";
20384 +mapsub = '/&lt;/&gt;/&amp;/&#39;/&quot;/';
20385 +nobreakspace = ' ' no_byte_order_mark = yes;
20386 +split = '<br/>';
20387 +output_type = 'xml';
20388 +indent=2;
20389 +
20390 +define event options_set;
20391 +    do / if $options['X'];
20392 +         set $xvar $options['X'];
20393 +    else;
20394 +         unset $xvar;
20395 +    done;
20396 +
20397 +    do / if $options['Y'];
20398 +         set $yvar $options['Y'];
20399 +    else;
20400 +         unset $yvar;
20401 +    done;
20402 +
20403 +    do / if $options['CATEGORY'];
20404 +         set $category $options['CATEGORY'];
20405 +    else;
20406 +         unset $category;
20407 +    done;
20408 +
20409 +    do / if $options['RESPONSE'];
20410 +         set $response $options['RESPONSE'];
20411 +    else;
20412 +         unset $response;
20413 +    done;
20414 +
20415 +    do / if $options['LIMITUPPER'];
20416 +         set $limitupper $options['LIMITUPPER'];
20417 +    else;
20418 +         unset $limitupper;
20419 +    done;
20420 +
20421 +    do / if $options['LIMITLOWER'];
20422 +         set $limitlower $options['LIMITLOWER'];
20423 +    else;
20424 +         unset $limitlower;
20425 +    done;
20426 +
20427 +    do / if $options['WIDTH'];
20428 +         set $graphwidth $options['WIDTH'];
20429 +    else;
20430 +         unset $graphwidth;
20431 +    done;
20432 +
20433 +    do / if $options['HEIGHT'];
20434 +         set $graphheight $options['HEIGHT'];
20435 +    else;
20436 +         unset $graphheight;
20437 +    done;
20438 +
20439 +    do / if $options['ROWS'];
20440 +         set $rows $options['ROWS'];
20441 +    else;
20442 +         unset $rows;
20443 +    done;
20444 +
20445 +    do / if $options['COLUMNS'];
20446 +         set $columns $options['COLUMNS'];
20447 +    else;
20448 +         unset $columns;
20449 +    done;
20450 +
20451 +    do / if $options['TITLE'];
20452 +         set $title $options['TITLE'];
20453 +    else;
20454 +         unset $title;
20455 +    done;
20456 +
20457 +    do / if $options['TITLE2'];
20458 +         set $title2 $options['TITLE2'];
20459 +    else;
20460 +         unset $title2;
20461 +    done;
20462 +
20463 +do / if $options['TITLE3'];
20464 +         set $title3 $options['TITLE3'];
20465 +    else;
20466 +         unset $title3;
20467 +    done;
20468 +
20469 +do / if $options['TITLE4'];
20470 +         set $title4 $options['TITLE4'];
20471 +    else;
20472 +         unset $title4;
20473 +    done;
20474 +
20475 +    do / if $options['ID'];
20476 +         set $id $options['ID'];
20477 +    else;
20478 +         unset $id;
20479 +    done;
20480 +
20481 +    do / if $options['PARENT'];
20482 +         set $parent $options['PARENT'];
20483 +    else;
20484 +         unset $parent;
20485 +    done;
20486 +
20487 +    do / if $options['PARENT'];
20488 +         set $parent $options['PARENT'];
20489 +    else;
20490 +         unset $parent;
20491 +    done;
20492 +
20493 +    do / if $options['ABOVETEXT'];
20494 +         set $abovetext $options['ABOVETEXT'];
20495 +    else;
20496 +         unset $abovetext;
20497 +    done;
20498 +
20499 +    do / if $options['BELOWTEXT'];
20500 +         set $belowtext $options['BELOWTEXT'];
20501 +    else;
20502 +         unset $belowtext;
20503 +    done;
20504 +
20505 +    do / if $options['LINKWIDTH'];
20506 +         set $linkwidth $options['LINKWIDTH'];
20507 +    else;
20508 +         unset $linkwidth;
20509 +    done;
20510 +
20511 +    do / if $options['NODECOLOR'];
20512 +         set $nodecolor $options['NODECOLOR'];
20513 +    else;
20514 +         unset $nodecolor;
20515 +    done;
20516 +
20517 +    do / if $options['NODESIZE'];
20518 +         set $nodesize $options['NODESIZE'];
20519 +    else;
20520 +         unset $nodesize;
20521 +    done;
20522 +
20523 +    do / if $options['NODETEXT'];
20524 +         set $nodetext $options['NODETEXT'];
20525 +    else;
20526 +         unset $nodetext;
20527 +    done;
20528 +
20529 +    do / if $options['PFDITEMS'];
20530 +         set $pfditems $options['PFDITEMS'];
20531 +    else;
20532 +         unset $pfditems;
20533 +    done;
20534 +
20535 +    do / if $options['GRIDSTYLE'];
20536 +         set $gridstyle $options['GRIDSTYLE'];
20537 +    else;
20538 +         unset $gridstyle;
20539 +    done;
20540 +
20541 +    do / if $options['GRIDHEIGHT'];
20542 +         set $gridheight $options['GRIDHEIGHT'];
20543 +    else;
20544 +         unset $gridheight;
20545 +    done;
20546 +
20547 +    do / if $options['GRIDWIDTH'];
20548 +         set $gridwidth $options['GRIDWIDTH'];
20549 +    else;
20550 +         unset $gridwidth;
20551 +    done;
20552 +
20553 +    do / if $options['ISGRIDVISIBLE'];
20554 +         set $isGridVisible $options['ISGRIDVISIBLE'];
20555 +    else;
20556 +         unset $isGridVisible;
20557 +    done;
20558 +
20559 +    do / if $options['LAYOUTACTIVATED'];
20560 +         set $layoutActivated $options['LAYOUTACTIVATED'];
20561 +    else;
20562 +         unset $layoutActivated;
20563 +    done;
20564 +
20565 +    do / if $options['PIELABELDISPLAY'];
20566 +         set $pieLabelDisplay $options['PIELABELDISPLAY'];
20567 +    else;
20568 +         unset $pieLabelDisplay;
20569 +    done;
20570 +
20571 +    do / if $options['COLORLIST'];
20572 +         set $colorlist $options['COLORLIST'];
20573 +    else;
20574 +         unset $colorlist;
20575 +    done;
20576 +
20577 +    do / if $options['COLORINDEX'];
20578 +         set $colorindex $options['COLORINDEX'];
20579 +    else;
20580 +         unset $colorindex;
20581 +    done;
20582 +
20583 +end;
20584 +
20585 +define event doc;
20586 +   eval $silkindex 0;
20587 +   eval $level 0;
20588 +end;
20589 +
20590 +define event GraphStyle;
20591 +   pure_style;
20592 +   trigger StatXMLDecl;
20593 +   put '<SASReport version="1.0" xmlns:style="http://www.sas.com/sasreportmodel/styles">' CR;
20594 +   put '<style:Scheme name="StatGraphScheme" version="9.2">' CR;
20595 +   trigger StatGraphScheme start;
20596 +   trigger StatDataDefaultScheme;
20597 +   trigger StatTwoColorRampScheme;
20598 +   trigger StatTwoColorAltRampScheme;
20599 +   trigger StatThreeColorRampScheme;
20600 +   trigger StatThreeColorAltRampScheme;
20601 +   trigger StatAxisLineScheme;
20602 +   trigger StatReferenceScheme;
20603 +   trigger StatGridLineScheme;
20604 +   trigger StatOutlineScheme;
20605 +   trigger StatBorderLineScheme;
20606 +   trigger StatTitleTextScheme;
20607 +   trigger StatFootnoteTextScheme;
20608 +   trigger StatDataTextScheme;
20609 +   trigger StatLabelTextScheme;
20610 +   trigger StatValueTextScheme;
20611 +   trigger StatUnicodeTextScheme;
20612 +   trigger StatBackFillScheme;
20613 +   trigger StatWallFillScheme;
20614 +   trigger StatFloorFillScheme;
20615 +   trigger StatLegendFillScheme;
20616 +   trigger StatHeaderFillScheme;
20617 +   trigger StatOutlierScheme;
20618 +   trigger StatFitScheme;
20619 +   trigger StatFit2Scheme;
20620 +   trigger StatPredictionScheme;
20621 +   trigger StatConfidenceScheme;
20622 +   trigger StatConfidence2Scheme;
20623 +   trigger StatPredictionLimitsScheme;
20624 +   trigger StatErrorScheme;
20625 +   trigger StatBoxMedianScheme;
20626 +   trigger StatBoxMeanScheme;
20627 +   trigger StatBoxWhiskerScheme;
20628 +   trigger StatBoxScheme;
20629 +   trigger StatHistogramScheme;
20630 +   trigger StatBandScheme;
20631 +   trigger StatContourScheme;
20632 +   trigger StatEllipseScheme;
20633 +   trigger StatAnnoLineScheme;
20634 +   trigger StatAnnoTextScheme;
20635 +   trigger StatAnnoShapeScheme;
20636 +   trigger StatSelectionScheme;
20637 +   trigger StatConnectLineScheme;
20638 +   trigger StatMissingScheme;
20639 +   trigger StatControlLimitsScheme;
20640 +   trigger StatRunTestScheme;
20641 +   trigger StatStarsScheme;
20642 +   trigger StatClippingScheme;
20643 +   trigger StatBlockScheme;
20644 +   trigger StatAltBlockScheme;
20645 +   trigger GraphDataStyle1;
20646 +   trigger GraphDataStyle2 / if !$stopDataStyles;
20647 +   trigger GraphDataStyle3 / if !$stopDataStyles;
20648 +   trigger GraphDataStyle4 / if !$stopDataStyles;
20649 +   trigger GraphDataStyle5 / if !$stopDataStyles;
20650 +   trigger GraphDataStyle6 / if !$stopDataStyles;
20651 +   trigger GraphDataStyle7 / if !$stopDataStyles;
20652 +   trigger GraphDataStyle8 / if !$stopDataStyles;
20653 +   trigger GraphDataStyle9 / if !$stopDataStyles;
20654 +   trigger GraphDataStyle10 / if !$stopDataStyles;
20655 +   trigger GraphDataStyle11 / if !$stopDataStyles;
20656 +   trigger GraphDataStyle12 / if !$stopDataStyles;
20657 +   trigger GraphDataStyle13 / if !$stopDataStyles;
20658 +   trigger GraphDataStyle14 / if !$stopDataStyles;
20659 +   trigger GraphDataStyle15 / if !$stopDataStyles;
20660 +   trigger StatGraphScheme finish;
20661 +   put '</style:Scheme>' CR;
20662 +   unset $stopDataStyles;
20663 +end;
20664 +
20665 +%macro dataStyleAttrs(index);
20666 +    do / if !any(LINESTYLE, CONTRASTCOLOR, MARKERSYMBOL, COLOR);
20667 +        set $stopDataStyles 'true';
20668 +        break;
20669 +    done;
20670 +    put '<style:Style context="GraphDataStyle' &index '"';
20671 +    putq " color=" COLOR;
20672 +    putq " altColor=" CONTRASTCOLOR;
20673 +    putq " fillColor=" COLOR;
20674 +    putq " textColor=" CONTRASTCOLOR;
20675 +    putq " lineStyle=" LINESTYLE;
20676 +    putq " lineColor=" CONTRASTCOLOR;
20677 +    putq " markerSymbol=" MARKERSYMBOL;
20678 +    putq " markerColor=" CONTRASTCOLOR;
20679 +    put "/>" NL;
20680 +%mend;
20681 +
20682 + define event GraphDataStyle1;
20683 +    pure_style;
20684 +    style=GraphData1;
20685 +    %dataStyleAttrs(1);
20686 + end;
20687 + define event GraphDataStyle2;
20688 +    pure_style;
20689 +    style=GraphData2;
20690 +    %dataStyleAttrs(2);
20691 + end;
20692 +define event GraphDataStyle3;
20693 +    pure_style;
20694 +    style=GraphData3;
20695 +    %dataStyleAttrs(3);
20696 + end;
20697 + define event GraphDataStyle4;
20698 +    pure_style;
20699 +    style=GraphData4;
20700 +    %dataStyleAttrs(4);
20701 + end;
20702 + define event GraphDataStyle5;
20703 +    pure_style;
20704 +    style=GraphData5;
20705 +    %dataStyleAttrs(5);
20706 + end;
20707 + define event GraphDataStyle6;
20708 +    pure_style;
20709 +    style=GraphData6;
20710 +    %dataStyleAttrs(6);
20711 + end;
20712 + define event GraphDataStyle7;
20713 +    pure_style;
20714 +    style=GraphData7;
20715 +    %dataStyleAttrs(7);
20716 + end;
20717 + define event GraphDataStyle8;
20718 +    pure_style;
20719 +    style=GraphData8;
20720 +    %dataStyleAttrs(8);
20721 + end;
20722 + define event GraphDataStyle9;
20723 +    pure_style;
20724 +    style=GraphData9;
20725 +    %dataStyleAttrs(9);
20726 + end;
20727 + define event GraphDataStyle10;
20728 +    pure_style;
20729 +    style=GraphData10;
20730 +    %dataStyleAttrs(10);
20731 + end;
20732 + define event GraphDataStyle11;
20733 +    pure_style;
20734 +    style=GraphData11;
20735 +    %dataStyleAttrs(11);
20736 + end;
20737 + define event GraphDataStyle12;
20738 +    pure_style;
20739 +    style=GraphData12;
20740 +    %dataStyleAttrs(12);
20741 + end;
20742 + define event GraphDataStyle13;
20743 +    pure_style;
20744 +    style=GraphData13;
20745 +    %dataStyleAttrs(13);
20746 + end;
20747 + define event GraphDataStyle14;
20748 +    pure_style;
20749 +    style=GraphData14;
20750 +    %dataStyleAttrs(14);
20751 + end;
20752 + define event GraphDataStyle15;
20753 +    pure_style;
20754 +    style=GraphData15;
20755 +    %dataStyleAttrs(15);
20756 + end;
20757 +
20758 +define event BeginGraph;
20759 +putlog "BeginGraph";
20760 +   trigger GraphStyle;
20761 +   put '<View>' CR;
20762 +   put '<StatGraph';
20763 +   put ' version="9.2_S2DVR"';
20764 +   putq " designHeight=" $graphheight;
20765 +   putq " designWidth=" $graphwidth;
20766 +   put ' style:style="StatGraphStyle';
20767 +   put anchor '"';
20768 +   put '>' CR;
20769 +   set $graph_started "true";
20770 +   eval $level 0;
20771 +end;
20772 +
20773 +define event LayoutOverlay;
20774 +   start:
20775 +      trigger BeginGraph / if !$graph_started;
20776 +      put '<EntryTitle><EntryItem>' $title '</EntryItem></EntryTitle>' CR / if $title;
20777 +      put '<EntryTitle><EntryItem>' $title2 '</EntryItem></EntryTitle>' CR / if $title2;
20778 +      put '<EntryTitle><EntryItem>' $title3'</EntryItem></EntryTitle>' CR / if $title3;
20779 +      put '<EntryTitle><EntryItem>' $title4 '</EntryItem></EntryTitle>' CR / if $title4;
20780 +      put '<LayoutOverlay>' CR;
20781 +      eval $level $level+1;
20782 +      unset $title;
20783 +      unset $title2;
20784 +      unset $title3;
20785 +      unset $title4;
20786 +   finish:
20787 +      put '</LayoutOverlay>' CR;
20788 +      eval $level $level-1;
20789 +      trigger EndGraph / if $level=0;
20790 +end;
20791 +
20792 +define event LayoutRegion;
20793 +   start:
20794 +      trigger BeginGraph / if !$graph_started;
20795 +      put '<EntryTitle><EntryItem>' $title '</EntryItem></EntryTitle>' CR / if $title;
20796 +      put '<EntryTitle><EntryItem>' $title2 '</EntryItem></EntryTitle>' CR / if $title2;
20797 +      put '<EntryTitle><EntryItem>' $title3'</EntryItem></EntryTitle>' CR / if $title3;
20798 +      put '<EntryTitle><EntryItem>' $title4 '</EntryItem></EntryTitle>' CR / if $title4;
20799 +      put '<LayoutRegion>' CR;
20800 +      eval $level $level+1;
20801 +      unset $title;
20802 +      unset $title2;
20803 +      unset $title3;
20804 +      unset $title4;
20805 +   finish:
20806 +      put '</LayoutRegion>' CR;
20807 +      eval $level $level-1;
20808 +      trigger EndGraph / if $level=0;
20809 +end;
20810 +
20811 +define event LayoutLattice;
20812 +   start:
20813 +      trigger BeginGraph / if !$graph_started;
20814 +      put '<LayoutLattice';
20815 +      putq " rows=" $rows;
20816 +      putq " columns=" $columns;
20817 +      put '>' CR;
20818 +      unset $rows;
20819 +      unset $columns;
20820 +      eval $level $level+1;
20821 +   finish:
20822 +      put '</LayoutLattice>' CR;
20823 +      eval $level $level-1;
20824 +      trigger EndGraph / if $level=0;
20825 +end;
20826 +
20827 +define event LayoutGridded;
20828 +   start:
20829 +      trigger BeginGraph / if !$graph_started;
20830 +      put '<LayoutGridded';
20831 +      putq " rows=" $rows;
20832 +      putq " columns=" $columns;
20833 +      put '>' CR;
20834 +      unset $rows;
20835 +      unset $columns;
20836 +      eval $level $level+1;
20837 +   finish:
20838 +      put '</LayoutGridded>' CR;
20839 +      eval $level $level-1;
20840 +      trigger EndGraph / if $level=0;
20841 +end;
20842 +
20843 +define event EndGraph;
20844 +   put '</StatGraph>' CR;
20845 +   put '</View>' CR;
20846 +   put "</SASReport>" CR;
20847 +   unset $graph_started;
20848 +end;
20849 +
20850 +define event ScatterPlot;
20851 +   put '<ScatterPlot';
20852 +   put ' x="StatGraphDataIDX#' $xvar '"' / if $xvar;
20853 +   put ' y="StatGraphDataIDX#' $yvar '"' / if $yvar;
20854 +   put '/>' CR;
20855 +   unset $xvar;
20856 +   unset $yvar;
20857 +end;
20858 +
20859 +define event SeriesPlot;
20860 +   put '<SeriesPlot';
20861 +   put ' x="StatGraphDataIDX#' $xvar '"' / if $xvar;
20862 +   put ' y="StatGraphDataIDX#' $yvar '"' / if $yvar;
20863 +   put '/>' CR;
20864 +   unset $xvar;
20865 +   unset $yvar;
20866 +end;
20867 +
20868 +define event BandPlot;
20869 +   put '<BandPlot';
20870 +   put ' x="StatGraphDataIDX#' $xvar '"' / if $xvar;
20871 +   put ' y="StatGraphDataIDX#' $yvar '"' / if $yvar;
20872 +   put ' limitUpper="StatGraphDataIDX#' $limitupper '"' / if $limitupper;
20873 +   put ' limitLower="StatGraphDataIDX#' $limitlower '"' / if $limitlower;
20874 +   putq " lineAttrs=" $style;
20875 +   put '/>' CR;
20876 +   unset $xvar;
20877 +   unset $yvar;
20878 +end;
20879 +
20880 +define event Pie;
20881 +   put '<Pie';
20882 +   put ' category="StatGraphDataIDX#' $category '"' / if $category;
20883 +   put ' response="StatGraphDataIDX#' $response '"' / if $response;
20884 +   putq " pieLabelComponents=" $pieLabelDisplay;
20885 +   put '/>' CR;
20886 +   unset $category;
20887 +   unset $response;
20888 +end;
20889 +
20890 +define event Bar;
20891 +   put '<Bar';
20892 +   put ' category="StatGraphDataIDX#' $category '"' / if $category;
20893 +   put ' response="StatGraphDataIDX#' $response '"' / if $response;
20894 +   put '/>' CR;
20895 +   unset $category;
20896 +   unset $response;
20897 +end;
20898 +
20899 +define event Histogram;
20900 +   put '<Histogram';
20901 +   put ' x="StatGraphDataIDX#' $xvar '"' / if $xvar;
20902 +   put '/>' CR;
20903 +   unset $xvar;
20904 +end;
20905 +
20906 +define event DecisionTree;
20907 +   put '<DecisionTree';
20908 +   put ' id="StatGraphDataIDX#' $id '"' / if $id;
20909 +   put ' parent="StatGraphDataIDX#' $parent '"' / if $parent;
20910 +   put ' aboveText="StatGraphDataIDX#' $abovetext '"' / if $abovetext;
20911 +   put ' belowText="StatGraphDataIDX#' $belowtext '"' / if $belowtext;
20912 +   put ' linkWidth="StatGraphDataIDX#' $linkwidth '"' / if $linkwidth;
20913 +   put ' nodeColor="StatGraphDataIDX#' $nodecolor '"' / if $nodecolor;
20914 +   put ' nodeText="StatGraphDataIDX#' $nodetext '"' / if $nodetext;
20915 +   put ' nodeSize="StatGraphDataIDX#' $nodesize '"' / if $nodesize;
20916 +   put ' fitToRegion="true"';
20917 +   put '/>' CR;
20918 +   unset $id;
20919 +   unset $parent;
20920 +   unset $abovetext;
20921 +   unset $belowtext;
20922 +   unset $linkwidth;
20923 +   unset $nodecolor;
20924 +   unset $nodetext;
20925 +end;
20926 +
20927 +define event Icicle;
20928 +   put '<Icicle';
20929 +   put ' id="StatGraphDataIDX#' $id '"' / if $id;
20930 +   put ' parent="StatGraphDataIDX#' $parent '"' / if $parent;
20931 +   put ' nodeSize="StatGraphDataIDX#' $nodesize '"' / if $nodesize;
20932 +   put ' nodeColor="StatGraphDataIDX#' $nodecolor '"' / if $nodecolor;
20933 +   put '/>' CR;
20934 +   unset $id;
20935 +   unset $parent;
20936 +   unset $nodecolor;
20937 +   unset $nodesize;
20938 +end;
20939 +
20940 +define event PFD;
20941 +   put '<PFD';
20942 +   putq " id=" $id / if $id;
20943 +   put ' pfditems="StatGraphDataIDX#' $pfdItems '"' / if $pfdItems;
20944 +   putq " isGridVisible=" $isgridvisible / if $isgridvisible;
20945 +   putq " gridHeight=" $gridheight / if $gridheight;
20946 +   putq " gridWidth=" $gridwidth / if $gridwidth;
20947 +   putq " gridStyle=" $gridstyle / if $gridstyle;
20948 +   putq " layoutActivated=" $layoutActivated / if $layoutActivated;
20949 +   put '/>' CR;
20950 +   unset $id;
20951 +   unset $parent;
20952 +   unset $nodecolor;
20953 +   unset $nodesize;
20954 +end;
20955 +
20956 +define event HeatMap;
20957 +   trigger BeginGraph / if !$graph_started;
20958 +   put '<HeatmapClassic';
20959 +   put ' x="StatGraphDataIDX#' $xvar '"' / if $xvar;
20960 +   put ' y="StatGraphDataIDX#' $yvar '"' / if $yvar;
20961 +   put ' response="StatGraphDataIDX#' $response '"' / if $response;
20962 +   putq ' colorList=' $colorlist / if $colorlist;
20963 +   putq ' colorIndex=' $colorindex / if $colorindex;
20964 +   put '/>' CR;
20965 +   trigger EndGraph / if $level=0;
20966 +   unset $xvar;
20967 +   unset $yvar;
20968 +   unset $response;
20969 +   unset $colorlist;
20970 +   unset $colorindex;
20971 +end;
20972 +
20973 +end;
NOTE: TAGSET 'Tagsets.Gtl' has been saved to: WORK.TEMPLAT
20974 +run;
NOTE: PROCEDURE TEMPLATE used (Total process time):
      real time           0.01 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              23409.84k
      OS Memory           33432.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     133
      Page Swaps                        0
      Voluntary Context Switches        4
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           128
 
 
20975 +
20976 +
20977 +
20978 +
20979 +
20980 +
20981 +/* data_template.sas */
20982 +proc template;
20983 +define statgraph dataonly / store=work.templat;
20984 +dynamic dummy1 dummy2 var1 var2 var3 var4 var5 var6 var7 var8 var9 var10;
20985 +begingraph;
20986 +if (0)
20987 +layout overlay;
20988 +   scatterplot x=dummy1 y=dummy2 /
20989 +      rolename=(a=var1 b=var2 c=var3 d=var4 e=var5 f=var6 g=var7 h=var8 i=var9 j=var10);
20990 +endlayout;
20991 +endif;
20992 +endgraph;
20993 +end;
NOTE: STATGRAPH 'Dataonly' has been saved to: WORK.TEMPLAT
20994 +run;
NOTE: PROCEDURE TEMPLATE used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              23409.84k
      OS Memory           33432.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     53
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           16
 
 
20995 +
NOTE: %INCLUDE (level 1) ending.
NOTE: Fileref TEMP has been deassigned.
 
NOTE: There were 1 observations read from the data set EMWS1.EM_NODEID.
      WHERE UPCASE(STRIP(nodeid))='REPORT';
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              23490.25k
      OS Memory           33432.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     78
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: The data set WORK.EM_USER_KEY has 1 observations and 9 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              23490.25k
      OS Memory           33432.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     86
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
20996  data emreportpath;
20997  length nodelabel $32 component $32;
20998  nodelabel="FIMPORT";
20999  component="FileImport";
21000  output;
21001  nodelabel="EMSave";
21002  component="EMSave";
21003  output;
21004  nodelabel="Trans";
21005  component="Transform";
21006  output;
21007  nodelabel="Smpl";
21008  component="Sample";
21009  output;
21010  nodelabel="CNTRL";
21011  component="";
21012  output;
21013  nodelabel="Part";
21014  component="Partition";
21015  output;
21016  nodelabel="CNTRL2";
21017  component="";
21018  output;
21019  nodelabel="Reg";
21020  component="Regression";
21021  output;
21022  nodelabel="DmineReg";
21023  component="DmineReg";
21024  output;
21025  nodelabel="CNTRL3";
21026  component="";
21027  output;
21028  nodelabel="Tree";
21029  component="DecisionTree";
21030  output;
21031  nodelabel="Tree2";
21032  component="DecisionTree";
21033  output;
21034  nodelabel="Tree3";
21035  component="DecisionTree";
21036  output;
21037  nodelabel="CNTRL4";
21038  component="";
21039  output;
21040  nodelabel="CNTRL5";
21041  component="";
21042  output;
21043  nodelabel="Ensmbl";
21044  component="Ensemble";
21045  output;
21046  nodelabel="Boost";
21047  component="Boost";
21048  output;
21049  nodelabel="MdlComp";
21050  component="ModelCompare";
21051  output;
21052  nodelabel="Score";
21053  component="Score";
21054  output;
21055  nodelabel="EMSave2";
21056  component="EMSave";
21057  output;
21058  run;
 
NOTE: The data set WORK.EMREPORTPATH has 20 observations and 2 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              24668.53k
      OS Memory           34172.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     152
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.01 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              24668.53k
      OS Memory           34172.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     332
      Page Swaps                        0
      Voluntary Context Switches        52
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 20 observations read from the data set WORK.EMREPORTPATH.
NOTE: The data set WORK.EMREPORTPATH has 20 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              24668.53k
      OS Memory           34172.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     127
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: STYLE 'Styles.Em_style' has been saved to: WORK.TEMPLAT
NOTE: PROCEDURE TEMPLATE used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              24668.53k
      OS Memory           34172.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     26
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
NOTE: Writing ODS PDF output to DISK destination
      "/home/u63387163/European Credit Card/Workspaces/EMWS1/Report/REPORT.pdf", printer "PDF".
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              27628.68k
      OS Memory           36220.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     56
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: 10 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.01 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              27958.65k
      OS Memory           36476.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     1213
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 1 observations read from the data set WORK.EM_USER_KEY.
NOTE: The data set WORK.EM_USER_KEY has 2 observations and 9 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              27958.65k
      OS Memory           36480.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     135
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: The file X is:
      Filename=/home/u63387163/European Credit Card/Workspaces/EMWS1/Report/PFD.xml,
      Owner Name=u63387163,Group Name=oda,
      Access Permission=-rw-r--r--,
      Last Modified=02Aug2024:03:27:51
 
NOTE: 27 records were written to the file X.
      The minimum record length was 60.
      The maximum record length was 85.
NOTE: There were 27 observations read from the data set EMWS1.EM_NODEID.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              27958.65k
      OS Memory           36480.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     89
      Page Swaps                        0
      Voluntary Context Switches        13
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: The file X is:
      Filename=/home/u63387163/European Credit Card/Workspaces/EMWS1/Report/PFD.xml,
      Owner Name=u63387163,Group Name=oda,
      Access Permission=-rw-r--r--,
      Last Modified=02Aug2024:03:27:51,
      File Size (bytes)=1932
 
NOTE: 29 records were written to the file X.
      The minimum record length was 53.
      The maximum record length was 59.
NOTE: There were 29 observations read from the data set EMWS1.EM_DGRAPH.
      WHERE TO not = ' ';
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              27958.65k
      OS Memory           36480.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     56
      Page Swaps                        0
      Voluntary Context Switches        11
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
NOTE: Fileref X has been deassigned.
 
NOTE: The infile XMLREF is:
      Filename=/home/u63387163/European Credit Card/Workspaces/EMWS1/Report/PFD.xml,
      Owner Name=u63387163,Group Name=oda,
      Access Permission=-rw-r--r--,
      Last Modified=02Aug2024:03:27:51,
      File Size (bytes)=3588
 
NOTE: 56 records were read from the infile XMLREF.
      The minimum record length was 53.
      The maximum record length was 85.
NOTE: The data set WORK.XMLDATA has 56 observations and 1 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              27958.65k
      OS Memory           36480.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     82
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 27 observations read from the data set EMWS1.EM_NODEID.
NOTE: The data set WORK.TEMP has 27 observations and 8 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              27958.65k
      OS Memory           36736.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     117
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: Writing TAGSETS.GTL Data file: /home/u63387163/European Credit
      Card/Workspaces/EMWS1/Report/data.xml
NOTE: Writing TAGSETS.GTL Body file: /home/u63387163/European Credit
      Card/Workspaces/EMWS1/Report/view.xml
 
WARNING: TAGSETS.GTL destination does not support images. Using the default format.
NOTE: There were 56 observations read from the data set WORK.XMLDATA.
NOTE: PROCEDURE SGRENDER used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              36307.12k
      OS Memory           45180.00k
      Timestamp           08/02/2024 03:27:51 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     512
      Page Swaps                        0
      Voluntary Context Switches        3
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           256
 
 
BeginGraph
 
NOTE: PROCEDURE SGSILK used (Total process time):
      real time           2.64 seconds
      user cpu time       0.08 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50008.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       2
      Page Reclaims                     3855
      Page Swaps                        0
      Voluntary Context Switches        485
      Involuntary Context Switches      2
      Block Input Operations            80
      Block Output Operations           464
 
 
NOTE: The query as specified involves ordering by an item that doesn't appear in its SELECT
      clause.
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50008.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     351
      Page Swaps                        0
      Voluntary Context Switches        15
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 1 observations read from the data set EMWS1.EM_NODEID.
      WHERE UPCASE(STRIP(nodeid))='FIMPORT';
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50008.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     66
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
21059  data _tmp_emrnp;
21060  length Parent $32 nodelabel $32 description $200 tooltype $32 component $32
21060! FlowScoreCodeFormat PublishScoreCode $16 PublishScoreCodeFormat $8 accumulatedScoreCode $1
21060! ;
21061  nodelabel="FIMPORT";
21062  parent=" ";
21063  description="Extension Class";
21064  component="FileImport";
21065  tooltype="SAMPLE";
21066  FlowScoreCodeFormat="DATASTEP";
21067  PublishScoreCode="EMPUBLISHSCORECODE";
21068  PublishScoreCodeFormat="DATASTEP";
21069  accumulatedScoreCode= "N";
21070  output;
21071  run;
 
NOTE: The data set WORK._TMP_EMRNP has 1 observations and 9 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50008.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     96
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50008.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     170
      Page Swaps                        0
      Voluntary Context Switches        12
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 1 observations read from the data set WORK._TMP_EMRNP.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50008.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     56
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: 4 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     478
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: 0 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: Fileref X has been deassigned.
 
21072  data properties;
21073  length Name $32 Value $200 Default $200;
21074  label NAME = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))" VALUE =
21074! "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel,    NOQUOTE))" DEFAULT =
21074! "%sysfunc(sasmsg(sashelp.dmine, rpt_default_vlabel,    NOQUOTE))";
21075  Name = 'ImportType';
21076  Value = "%nrbquote(Local)";
21077  Default = "%nrbquote(LOCAL)";
21078  output;
21079  Name = 'GuessRows';
21080  Value = "%nrbquote(500)";
21081  Default = "%nrbquote()";
21082  output;
21083  Name = 'Delimiter';
21084  Value = "%nrbquote(,)";
21085  Default = "%nrbquote()";
21086  output;
21087  Name = 'NameRow';
21088  Value = "%nrbquote(Y)";
21089  Default = "%nrbquote()";
21090  output;
21091  Name = 'SkipRows';
21092  Value = "%nrbquote(0)";
21093  Default = "%nrbquote()";
21094  output;
21095  Name = 'MaxRows';
21096  Value = "%nrbquote(1000000)";
21097  Default = "%nrbquote()";
21098  output;
21099  Name = 'MaxCols';
21100  Value = "%nrbquote(10000)";
21101  Default = "%nrbquote()";
21102  output;
21103  Name = 'FileType';
21104  Value = "%nrbquote(csv)";
21105  Default = "%nrbquote(XLS)";
21106  output;
21107  Name = 'Role';
21108  Value = "%nrbquote(TRAIN)";
21109  Default = "%nrbquote()";
21110  output;
21111  Name = 'Summarize';
21112  Value = "%nrbquote(N)";
21113  Default = "%nrbquote()";
21114  output;
21115  Name = 'AdvancedAdvisor';
21116  Value = "%nrbquote(N)";
21117  Default = "%nrbquote()";
21118  output;
21119  Name = 'IFileName';
21120  Value = "%nrbquote(C:\Users\Owner\OneDrive\Desktop\CSU GLOBAL\European Credit Card.csv)";
21121  Default = "%nrbquote()";
21122  output;
21123  Name = 'AccessTable';
21124  Value = "%nrbquote(NoTableName)";
21125  Default = "%nrbquote()";
21126  output;
21127  Name = 'UserID';
21128  Value = "%nrbquote(NoUserID)";
21129  Default = "%nrbquote()";
21130  output;
21131  Name = 'Password';
21132  Value = "%nrbquote(NoPassword)";
21133  Default = "%nrbquote()";
21134  output;
21135  run;
 
NOTE: The data set WORK.PROPERTIES has 15 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     433
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21136  proc sort data=properties;
21137  by Name;
21138  run;
 
NOTE: There were 15 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK.PROPERTIES has 15 observations and 3 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     115
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21139  data temp;
21140  length Name $32 Value $200 Default $200;
21141  label NAME = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))" VALUE =
21141! "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel,    NOQUOTE))";
21142  Name = 'Component';
21143  Value = "FileImport";
21144  Default="";
21145  run;
 
NOTE: The data set WORK.TEMP has 1 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     313
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21146  data properties;
21147  set temp properties;
21148  run;
 
NOTE: There were 1 observations read from the data set WORK.TEMP.
NOTE: There were 15 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK.PROPERTIES has 16 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     156
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.01 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     1129
      Page Swaps                        0
      Voluntary Context Switches        19
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           1072
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: The data set WORK._TMPMS_CONTENT has 3 observations and 1 variables.
NOTE: PROCEDURE CONTENTS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     153
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           272
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 16 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK._TMPMS_DAT1 has 5 observations and 4 variables.
NOTE: The data set WORK._TMPMS_DAT2 has 5 observations and 4 variables.
NOTE: The data set WORK._TMPMS_DAT3 has 5 observations and 4 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     248
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           792
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT1.
NOTE: The data set WORK._TMPMS_DAT1 has 5 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     114
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT2.
NOTE: The data set WORK._TMPMS_DAT2 has 5 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT3.
NOTE: The data set WORK._TMPMS_DAT3 has 5 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT1.
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT2.
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT3.
NOTE: The data set WORK._TMPMS_ALLDAT has 5 observations and 10 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     223
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_ALLDAT.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     80
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Deleting WORK._TMPMS_ALLDAT (memtype=DATA).
NOTE: Deleting WORK._TMPMS_CONTENT (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT1 (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT2 (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT3 (memtype=DATA).
 
NOTE: PROCEDURE DATASETS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     59
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: Deleting WORK.PROPERTIES (memtype=DATA).
NOTE: PROCEDURE DELETE used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     15
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Numeric variables in the input data set will be converted to character in the output data
      set.
NOTE: There were 1 observations read from the data set EMWS1.FIMPORT_EMTMETADEFAULT.
NOTE: The data set WORK.TEMP_SOURCE has 15 observations and 2 variables.
NOTE: PROCEDURE TRANSPOSE used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     217
      Page Swaps                        0
      Voluntary Context Switches        11
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           528
 
 
 
NOTE: There were 15 observations read from the data set WORK.TEMP_SOURCE.
NOTE: The data set WORK.TEMP_SOURCE has 12 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.01 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     1715
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: The data set WORK._TMPMS_CONTENT has 3 observations and 1 variables.
NOTE: PROCEDURE CONTENTS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     149
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           272
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 12 observations read from the data set WORK.TEMP_SOURCE.
NOTE: The data set WORK._TMPMS_DAT1 has 4 observations and 3 variables.
NOTE: The data set WORK._TMPMS_DAT2 has 4 observations and 3 variables.
NOTE: The data set WORK._TMPMS_DAT3 has 4 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     247
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           792
 
 
 
NOTE: There were 4 observations read from the data set WORK._TMPMS_DAT1.
NOTE: The data set WORK._TMPMS_DAT1 has 4 observations and 3 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 4 observations read from the data set WORK._TMPMS_DAT2.
NOTE: The data set WORK._TMPMS_DAT2 has 4 observations and 3 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 4 observations read from the data set WORK._TMPMS_DAT3.
NOTE: The data set WORK._TMPMS_DAT3 has 4 observations and 3 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 4 observations read from the data set WORK._TMPMS_DAT1.
NOTE: There were 4 observations read from the data set WORK._TMPMS_DAT2.
NOTE: There were 4 observations read from the data set WORK._TMPMS_DAT3.
NOTE: The data set WORK._TMPMS_ALLDAT has 4 observations and 7 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     221
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 4 observations read from the data set WORK._TMPMS_ALLDAT.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     53
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Deleting WORK._TMPMS_ALLDAT (memtype=DATA).
NOTE: Deleting WORK._TMPMS_CONTENT (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT1 (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT2 (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT3 (memtype=DATA).
 
NOTE: PROCEDURE DATASETS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     48
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: Deleting WORK.TEMP_SOURCE (memtype=DATA).
NOTE: PROCEDURE DELETE used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50048.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     15
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Variable length is uninitialized.
NOTE: There were 31 observations read from the data set EMWS1.FIMPORT_EMTRAINVARIABLE.
NOTE: The data set WORK._TMPVL_PDV has 31 observations and 6 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     573
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      2
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 31 observations read from the data set WORK._TMPVL_PDV.
NOTE: The data set WORK._TMPVL_PDV has 31 observations and 6 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     111
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 31 observations read from the data set EMWS1.FIMPORT_VARIABLESET.
NOTE: The data set WORK._TMPVL_VS has 31 observations and 21 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     143
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           272
 
 
 
NOTE: There were 31 observations read from the data set WORK._TMPVL_PDV.
NOTE: There were 31 observations read from the data set WORK._TMPVL_VS.
NOTE: The data set WORK._TMPVL_PDV has 31 observations and 21 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     720
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: There were 31 observations read from the data set WORK._TMPVL_PDV.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.01 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     68
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.FIMPORT_CMETA_TRAIN.
      WHERE UPCASE(creator)='FIMPORT';
NOTE: The data set WORK._TMPVL_PDVX has 0 observations and 20 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     803
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: No observations in data set WORK._TMPVL_PDVX.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     50
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Deleting WORK._TMPVL_PDV (memtype=DATA).
NOTE: Deleting WORK._TMPVL_PDVX (memtype=DATA).
NOTE: Deleting WORK._TMPVL_VS (memtype=DATA).
 
NOTE: PROCEDURE DATASETS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     48
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     53
      Page Swaps                        0
      Voluntary Context Switches        12
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     223
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      2
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=1;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==1 : Viewtype = PROPERTIES SETTINGS
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     32
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=2;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==2 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=3;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==3 : Viewtype = DATA VARIABLES
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=4;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==4 : Viewtype = TEXT TOOLLOG
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=5;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==5 : Viewtype = TEXT TOOLOUTPUT
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=6;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==6 : Viewtype = TEXT TOOLTRAINCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=7;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==7 : Viewtype = TEXT NOTES
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=8;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==8 : Viewtype = TEXT FLOWCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=9;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==9 : Viewtype = TEXT SASCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=10;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==10 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=11;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==11 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=12;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==12 : Viewtype = DATA EMSTAT
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 17 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=13;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==13 : Viewtype = LATTICE INTERVALTARGET
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 16 observations read from the data set EMWS1.FIMPORT_EMREPORT.
      WHERE id=14;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==14 : Viewtype = LATTICE CLASSTARGET
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 1 observations read from the data set EMWS1.EM_NODEID.
      WHERE UPCASE(STRIP(nodeid))='EMSAVE';
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     59
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
21149  data _tmp_emrnp;
21150  length Parent $32 nodelabel $32 description $200 tooltype $32 component $32
21150! FlowScoreCodeFormat PublishScoreCode $16 PublishScoreCodeFormat $8 accumulatedScoreCode $1
21150! ;
21151  nodelabel="FIMPORT";
21152  parent=" ";
21153  description="Extension Class";
21154  component="FileImport";
21155  tooltype="SAMPLE";
21156  FlowScoreCodeFormat="DATASTEP";
21157  PublishScoreCode="EMPUBLISHSCORECODE";
21158  PublishScoreCodeFormat="DATASTEP";
21159  accumulatedScoreCode= "N";
21160  output;
21161  nodelabel="EMSave";
21162  parent="FIMPORT";
21163  description="Extension Class";
21164  component="EMSave";
21165  tooltype="UTILITY";
21166  FlowScoreCodeFormat="DATASTEP";
21167  PublishScoreCode="EMPUBLISHSCORECODE";
21168  PublishScoreCodeFormat="DATASTEP";
21169  accumulatedScoreCode= "N";
21170  output;
21171  run;
 
NOTE: The data set WORK._TMP_EMRNP has 2 observations and 9 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     105
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     175
      Page Swaps                        0
      Voluntary Context Switches        15
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 2 observations read from the data set WORK._TMP_EMRNP.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50304.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: 4 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     480
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: 0 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     25
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: Fileref X has been deassigned.
 
21172  data properties;
21173  length Name $32 Value $200 Default $200;
21174  label NAME = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))" VALUE =
21174! "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel,    NOQUOTE))" DEFAULT =
21174! "%sysfunc(sasmsg(sashelp.dmine, rpt_default_vlabel,    NOQUOTE))";
21175  Name = 'Type';
21176  Value = "%nrbquote(SAS7BDAT)";
21177  Default = "%nrbquote(SAS7DBAT)";
21178  output;
21179  Name = 'Replace';
21180  Value = "%nrbquote(Y)";
21181  Default = "%nrbquote()";
21182  output;
21183  Name = 'AllObs';
21184  Value = "%nrbquote(Y)";
21185  Default = "%nrbquote()";
21186  output;
21187  Name = 'OutObs';
21188  Value = "%nrbquote(1000)";
21189  Default = "%nrbquote()";
21190  output;
21191  Name = 'AllRoles';
21192  Value = "%nrbquote(Y)";
21193  Default = "%nrbquote()";
21194  output;
21195  Name = 'Train';
21196  Value = "%nrbquote(Y)";
21197  Default = "%nrbquote()";
21198  output;
21199  Name = 'Validate';
21200  Value = "%nrbquote(Y)";
21201  Default = "%nrbquote()";
21202  output;
21203  Name = 'Test';
21204  Value = "%nrbquote(Y)";
21205  Default = "%nrbquote()";
21206  output;
21207  Name = 'Score';
21208  Value = "%nrbquote(Y)";
21209  Default = "%nrbquote()";
21210  output;
21211  Name = 'Transaction';
21212  Value = "%nrbquote(Y)";
21213  Default = "%nrbquote()";
21214  output;
21215  Name = 'Lib';
21216  Value = "%nrbquote(EUROPEAN)";
21217  Default = "%nrbquote()";
21218  output;
21219  Name = 'DirectorySelector';
21220  Value = "%nrbquote()";
21221  Default = "%nrbquote()";
21222  output;
21223  Name = 'Name';
21224  Value = "%nrbquote(European Credit Card_Data)";
21225  Default = "%nrbquote()";
21226  output;
21227  run;
 
NOTE: The data set WORK.PROPERTIES has 13 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     441
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21228  proc sort data=properties;
21229  by Name;
21230  run;
 
NOTE: There were 13 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK.PROPERTIES has 13 observations and 3 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     112
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21231  data temp;
21232  length Name $32 Value $200 Default $200;
21233  label NAME = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))" VALUE =
21233! "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel,    NOQUOTE))";
21234  Name = 'Component';
21235  Value = "EMSave";
21236  Default="";
21237  run;
 
NOTE: The data set WORK.TEMP has 1 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     313
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21238  data properties;
21239  set temp properties;
21240  run;
 
NOTE: There were 1 observations read from the data set WORK.TEMP.
NOTE: There were 13 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK.PROPERTIES has 14 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     156
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.01 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     1130
      Page Swaps                        0
      Voluntary Context Switches        19
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           1064
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: The data set WORK._TMPMS_CONTENT has 3 observations and 1 variables.
NOTE: PROCEDURE CONTENTS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     149
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           272
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 14 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK._TMPMS_DAT1 has 5 observations and 4 variables.
NOTE: The data set WORK._TMPMS_DAT2 has 5 observations and 4 variables.
NOTE: The data set WORK._TMPMS_DAT3 has 4 observations and 4 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     247
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           792
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT1.
NOTE: The data set WORK._TMPMS_DAT1 has 5 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT2.
NOTE: The data set WORK._TMPMS_DAT2 has 5 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 4 observations read from the data set WORK._TMPMS_DAT3.
NOTE: The data set WORK._TMPMS_DAT3 has 4 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT1.
NOTE: There were 5 observations read from the data set WORK._TMPMS_DAT2.
NOTE: There were 4 observations read from the data set WORK._TMPMS_DAT3.
NOTE: The data set WORK._TMPMS_ALLDAT has 5 observations and 10 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     186
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 5 observations read from the data set WORK._TMPMS_ALLDAT.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     54
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Deleting WORK._TMPMS_ALLDAT (memtype=DATA).
NOTE: Deleting WORK._TMPMS_CONTENT (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT1 (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT2 (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT3 (memtype=DATA).
 
NOTE: PROCEDURE DATASETS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     48
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: Deleting WORK.PROPERTIES (memtype=DATA).
NOTE: PROCEDURE DELETE used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     15
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Variable length is uninitialized.
NOTE: There were 31 observations read from the data set EMWS1.EMSAVE_EMTRAINVARIABLE.
NOTE: The data set WORK._TMPVL_PDV has 30 observations and 5 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     573
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 30 observations read from the data set WORK._TMPVL_PDV.
NOTE: The data set WORK._TMPVL_PDV has 30 observations and 5 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     111
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 30 observations read from the data set WORK._TMPVL_PDV.
NOTE: The data set WORK._TMPVL_PDV has 30 observations and 5 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     111
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
WARNING: Length of character variable role has already been set.
         Use the LENGTH statement as the very first statement in the DATA STEP to declare the
         length of a character variable.
 
NOTE: There were 30 observations read from the data set WORK._TMPVL_PDV.
NOTE: The data set WORK._TMPVL_PDS has 1 observations and 4 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     576
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 1 observations read from the data set WORK._TMPVL_PDS.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     54
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.EMSAVE_CMETA_TRAIN.
      WHERE UPCASE(creator)='EMSave';
NOTE: The data set WORK._TMPVL_PDVX has 0 observations and 20 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     803
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: Input data set is empty.
NOTE: The data set WORK._TMPVL_PDVX has 0 observations and 20 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
WARNING: Length of character variable role has already been set.
         Use the LENGTH statement as the very first statement in the DATA STEP to declare the
         length of a character variable.
 
NOTE: There were 0 observations read from the data set WORK._TMPVL_PDVX.
NOTE: The data set WORK._TMPVL_PDVXP has 0 observations and 4 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     575
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Deleting WORK._TMPVL_PDS (memtype=DATA).
NOTE: Deleting WORK._TMPVL_PDV (memtype=DATA).
NOTE: Deleting WORK._TMPVL_PDVX (memtype=DATA).
NOTE: Deleting WORK._TMPVL_PDVXP (memtype=DATA).
 
NOTE: PROCEDURE DATASETS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     48
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        12
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     88
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=1;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==1 : Viewtype = PROPERTIES SETTINGS
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     22
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=2;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==2 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=3;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==3 : Viewtype = DATA VARIABLES
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=4;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==4 : Viewtype = TEXT TOOLLOG
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=5;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==5 : Viewtype = TEXT TOOLOUTPUT
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=6;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:54 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==6 : Viewtype = TEXT TOOLTRAINCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=7;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==7 : Viewtype = TEXT NOTES
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=8;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==8 : Viewtype = TEXT FLOWCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=9;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==9 : Viewtype = TEXT SASCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=10;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==10 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=11;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==11 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.EMSAVE_EMREPORT.
      WHERE id=12;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==12 : Viewtype = DATA DATA
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: There were 1 observations read from the data set EMWS1.EMSAVE_SAVEOUT.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     54
      Page Swaps                        0
      Voluntary Context Switches        12
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 1 observations read from the data set EMWS1.EM_NODEID.
      WHERE UPCASE(STRIP(nodeid))='TRANS';
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     62
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
21241  data _tmp_emrnp;
21242  length Parent $32 nodelabel $32 description $200 tooltype $32 component $32
21242! FlowScoreCodeFormat PublishScoreCode $16 PublishScoreCodeFormat $8 accumulatedScoreCode $1
21242! ;
21243  nodelabel="FIMPORT";
21244  parent=" ";
21245  description="Extension Class";
21246  component="FileImport";
21247  tooltype="SAMPLE";
21248  FlowScoreCodeFormat="DATASTEP";
21249  PublishScoreCode="EMPUBLISHSCORECODE";
21250  PublishScoreCodeFormat="DATASTEP";
21251  accumulatedScoreCode= "N";
21252  output;
21253  nodelabel="EMSave";
21254  parent="FIMPORT";
21255  description="Extension Class";
21256  component="EMSave";
21257  tooltype="UTILITY";
21258  FlowScoreCodeFormat="DATASTEP";
21259  PublishScoreCode="EMPUBLISHSCORECODE";
21260  PublishScoreCodeFormat="DATASTEP";
21261  accumulatedScoreCode= "N";
21262  output;
21263  nodelabel="Trans";
21264  parent="EMSave";
21265  description="Transform";
21266  component="Transform";
21267  tooltype="MODIFY";
21268  FlowScoreCodeFormat="DATASTEP";
21269  PublishScoreCode="EMPUBLISHSCORECODE";
21270  PublishScoreCodeFormat="DATASTEP";
21271  accumulatedScoreCode= "N";
21272  output;
21273  run;
 
NOTE: The data set WORK._TMP_EMRNP has 3 observations and 9 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     87
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     154
      Page Swaps                        0
      Voluntary Context Switches        18
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMP_EMRNP.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: 4 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     478
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: 0 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
NOTE: Fileref X has been deassigned.
 
21274  data properties;
21275  length Name $32 Value $200 Default $200;
21276  label NAME = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))" VALUE =
21276! "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel,    NOQUOTE))" DEFAULT =
21276! "%sysfunc(sasmsg(sashelp.dmine, rpt_default_vlabel,    NOQUOTE))";
21277  Name = 'DefaultMethod';
21278  Value = "%nrbquote(NONE)";
21279  Default = "%nrbquote()";
21280  output;
21281  Name = 'DefaultTargetMethod';
21282  Value = "%nrbquote(NONE)";
21283  Default = "%nrbquote()";
21284  output;
21285  Name = 'DefaultClassMethod';
21286  Value = "%nrbquote(NONE)";
21287  Default = "%nrbquote()";
21288  output;
21289  Name = 'DefaultClassTargetMethod';
21290  Value = "%nrbquote(NONE)";
21291  Default = "%nrbquote()";
21292  output;
21293  Name = 'Offset';
21294  Value = "%nrbquote(1)";
21295  Default = "%nrbquote()";
21296  output;
21297  Name = 'MinOffset';
21298  Value = "%nrbquote(Y)";
21299  Default = "%nrbquote()";
21300  output;
21301  Name = 'HideVariable';
21302  Value = "%nrbquote(Y)";
21303  Default = "%nrbquote()";
21304  output;
21305  Name = 'RejectVariable';
21306  Value = "%nrbquote(Y)";
21307  Default = "%nrbquote()";
21308  output;
21309  Name = 'GroupCutoff';
21310  Value = "%nrbquote(0.1)";
21311  Default = "%nrbquote()";
21312  output;
21313  Name = 'GroupMissing';
21314  Value = "%nrbquote(N)";
21315  Default = "%nrbquote()";
21316  output;
21317  Name = 'EmRandomSeed';
21318  Value = "%nrbquote(12345)";
21319  Default = "%nrbquote()";
21320  output;
21321  Name = 'EmSampleSize';
21322  Value = "%nrbquote(DEFAULT)";
21323  Default = "%nrbquote()";
21324  output;
21325  Name = 'EmSampleMethod';
21326  Value = "%nrbquote(FIRSTN)";
21327  Default = "%nrbquote()";
21328  output;
21329  Name = 'MissingValue';
21330  Value = "%nrbquote(USEINSEARCH)";
21331  Default = "%nrbquote()";
21332  output;
21333  Name = 'SummaryVariables';
21334  Value = "%nrbquote(TRANSFORMED)";
21335  Default = "%nrbquote()";
21336  output;
21337  Name = 'SummaryStatistics';
21338  Value = "%nrbquote(Y)";
21339  Default = "%nrbquote()";
21340  output;
21341  Name = 'UseMetaTransform';
21342  Value = "%nrbquote(Y)";
21343  Default = "%nrbquote()";
21344  output;
21345  Name = 'MissingAsLevel';
21346  Value = "%nrbquote(N)";
21347  Default = "%nrbquote()";
21348  output;
21349  Name = 'NumberofBins';
21350  Value = "%nrbquote(VARIABLES)";
21351  Default = "%nrbquote()";
21352  output;
21353  Name = 'MaxOptimalBins';
21354  Value = "%nrbquote(4)";
21355  Default = "%nrbquote()";
21356  output;
21357  run;
 
NOTE: The data set WORK.PROPERTIES has 20 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     438
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21358  proc sort data=properties;
21359  by Name;
21360  run;
 
NOTE: There were 20 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK.PROPERTIES has 20 observations and 3 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     112
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21361  data temp;
21362  length Name $32 Value $200 Default $200;
21363  label NAME = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))" VALUE =
21363! "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel,    NOQUOTE))";
21364  Name = 'Component';
21365  Value = "Transform";
21366  Default="";
21367  run;
 
NOTE: The data set WORK.TEMP has 1 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     313
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21368  data properties;
21369  set temp properties;
21370  run;
 
NOTE: There were 1 observations read from the data set WORK.TEMP.
NOTE: There were 20 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK.PROPERTIES has 21 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     155
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.01 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     1121
      Page Swaps                        0
      Voluntary Context Switches        19
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           1072
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: The data set WORK._TMPMS_CONTENT has 3 observations and 1 variables.
NOTE: PROCEDURE CONTENTS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     149
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           272
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 21 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK._TMPMS_DAT1 has 7 observations and 4 variables.
NOTE: The data set WORK._TMPMS_DAT2 has 7 observations and 4 variables.
NOTE: The data set WORK._TMPMS_DAT3 has 7 observations and 4 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     247
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           792
 
 
 
NOTE: There were 7 observations read from the data set WORK._TMPMS_DAT1.
NOTE: The data set WORK._TMPMS_DAT1 has 7 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 7 observations read from the data set WORK._TMPMS_DAT2.
NOTE: The data set WORK._TMPMS_DAT2 has 7 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 7 observations read from the data set WORK._TMPMS_DAT3.
NOTE: The data set WORK._TMPMS_DAT3 has 7 observations and 4 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 7 observations read from the data set WORK._TMPMS_DAT1.
NOTE: There were 7 observations read from the data set WORK._TMPMS_DAT2.
NOTE: There were 7 observations read from the data set WORK._TMPMS_DAT3.
NOTE: The data set WORK._TMPMS_ALLDAT has 7 observations and 10 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     220
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 7 observations read from the data set WORK._TMPMS_ALLDAT.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     54
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Deleting WORK._TMPMS_ALLDAT (memtype=DATA).
NOTE: Deleting WORK._TMPMS_CONTENT (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT1 (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT2 (memtype=DATA).
NOTE: Deleting WORK._TMPMS_DAT3 (memtype=DATA).
 
NOTE: PROCEDURE DATASETS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     48
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: Deleting WORK.PROPERTIES (memtype=DATA).
NOTE: PROCEDURE DELETE used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     15
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Variable length is uninitialized.
NOTE: There were 31 observations read from the data set EMWS1.TRANS_EMTRAINVARIABLE.
NOTE: The data set WORK._TMPVL_PDV has 31 observations and 7 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     573
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 31 observations read from the data set WORK._TMPVL_PDV.
NOTE: The data set WORK._TMPVL_PDV has 31 observations and 7 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     111
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 31 observations read from the data set WORK._TMPVL_PDV.
NOTE: The data set WORK._TMPVL_PDV has 31 observations and 7 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     111
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
WARNING: Length of character variable role has already been set.
         Use the LENGTH statement as the very first statement in the DATA STEP to declare the
         length of a character variable.
 
2 OUTPUT;
NOTE: There were 31 observations read from the data set WORK._TMPVL_PDV.
NOTE: The data set WORK._TMPVL_PDS has 2 observations and 4 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     576
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 2 observations read from the data set WORK._TMPVL_PDS.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     54
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.TRANS_CMETA_TRAIN.
      WHERE UPCASE(creator)='Trans';
NOTE: The data set WORK._TMPVL_PDVX has 0 observations and 20 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     803
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: Input data set is empty.
NOTE: The data set WORK._TMPVL_PDVX has 0 observations and 20 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     113
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
WARNING: Length of character variable role has already been set.
         Use the LENGTH statement as the very first statement in the DATA STEP to declare the
         length of a character variable.
 
NOTE: There were 0 observations read from the data set WORK._TMPVL_PDVX.
NOTE: The data set WORK._TMPVL_PDVXP has 0 observations and 4 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     575
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: Deleting WORK._TMPVL_PDS (memtype=DATA).
NOTE: Deleting WORK._TMPVL_PDV (memtype=DATA).
NOTE: Deleting WORK._TMPVL_PDVX (memtype=DATA).
NOTE: Deleting WORK._TMPVL_PDVXP (memtype=DATA).
 
NOTE: PROCEDURE DATASETS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     48
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        13
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     88
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=1;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           8
 
 
==1 : Viewtype = PROPERTIES SETTINGS
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=2;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==2 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=3;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==3 : Viewtype = DATA VARIABLES
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=4;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==4 : Viewtype = TEXT TOOLLOG
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=5;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==5 : Viewtype = TEXT TOOLOUTPUT
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=6;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==6 : Viewtype = TEXT TOOLTRAINCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=7;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==7 : Viewtype = TEXT NOTES
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=8;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==8 : Viewtype = TEXT FLOWCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=9;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
==9 : Viewtype = TEXT SASCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=10;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==10 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 0 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=11;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==11 : Viewtype =
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=12;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==12 : Viewtype = DATA FORMULA
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=13;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==13 : Viewtype = DATA COMPUTED
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=14;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==14 : Viewtype = DATA METATRANS
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=15;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==15 : Viewtype = TEXT USERCODE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=16;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==16 : Viewtype = DATA BESTRSQUARE
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 6 observations read from the data set EMWS1.TRANS_EMREPORT.
      WHERE id=17;
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
==17 : Viewtype = DATA STATISTICS
 
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: There were 2 observations read from the data set EMWS1.TRANS_STATISTICS.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     54
      Page Swaps                        0
      Voluntary Context Switches        12
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 1 observations read from the data set EMWS1.EM_NODEID.
      WHERE UPCASE(STRIP(nodeid))='SMPL';
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
21371  data _tmp_emrnp;
21372  length Parent $32 nodelabel $32 description $200 tooltype $32 component $32
21372! FlowScoreCodeFormat PublishScoreCode $16 PublishScoreCodeFormat $8 accumulatedScoreCode $1
21372! ;
21373  nodelabel="FIMPORT";
21374  parent=" ";
21375  description="Extension Class";
21376  component="FileImport";
21377  tooltype="SAMPLE";
21378  FlowScoreCodeFormat="DATASTEP";
21379  PublishScoreCode="EMPUBLISHSCORECODE";
21380  PublishScoreCodeFormat="DATASTEP";
21381  accumulatedScoreCode= "N";
21382  output;
21383  nodelabel="EMSave";
21384  parent="FIMPORT";
21385  description="Extension Class";
21386  component="EMSave";
21387  tooltype="UTILITY";
21388  FlowScoreCodeFormat="DATASTEP";
21389  PublishScoreCode="EMPUBLISHSCORECODE";
21390  PublishScoreCodeFormat="DATASTEP";
21391  accumulatedScoreCode= "N";
21392  output;
21393  nodelabel="Trans";
21394  parent="EMSave";
21395  description="Transform";
21396  component="Transform";
21397  tooltype="MODIFY";
21398  FlowScoreCodeFormat="DATASTEP";
21399  PublishScoreCode="EMPUBLISHSCORECODE";
21400  PublishScoreCodeFormat="DATASTEP";
21401  accumulatedScoreCode= "N";
21402  output;
21403  nodelabel="Smpl";
21404  parent="Trans";
21405  description="Sampling  Class";
21406  component="Sample";
21407  tooltype="SAMPLE";
21408  FlowScoreCodeFormat="DATASTEP";
21409  PublishScoreCode="EMPUBLISHSCORECODE";
21410  PublishScoreCodeFormat="DATASTEP";
21411  accumulatedScoreCode= "N";
21412  output;
21413  run;
 
NOTE: The data set WORK._TMP_EMRNP has 4 observations and 9 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     89
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     156
      Page Swaps                        0
      Voluntary Context Switches        21
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
 
NOTE: There were 4 observations read from the data set WORK._TMP_EMRNP.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: PROCEDURE SQL used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.01 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     52
      Page Swaps                        0
      Voluntary Context Switches        7
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: 4 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     478
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: 0 lines were written to file PRINT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     21
      Page Swaps                        0
      Voluntary Context Switches        8
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
NOTE: Fileref X has been deassigned.
 
21414  data properties;
21415  length Name $32 Value $200 Default $200;
21416  label NAME = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))" VALUE =
21416! "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel,    NOQUOTE))" DEFAULT =
21416! "%sysfunc(sasmsg(sashelp.dmine, rpt_default_vlabel,    NOQUOTE))";
21417  Name = 'Method';
21418  Value = "%nrbquote(STRATIFY)";
21419  Default = "%nrbquote(DEFAULT)";
21420  output;
21421  Name = 'SizeType';
21422  Value = "%nrbquote(PERCENT)";
21423  Default = "%nrbquote()";
21424  output;
21425  Name = 'SizePercent';
21426  Value = "%nrbquote(10)";
21427  Default = "%nrbquote()";
21428  output;
21429  Name = 'SizeObs';
21430  Value = "%nrbquote(.)";
21431  Default = "%nrbquote()";
21432  output;
21433  Name = 'RandomSeed';
21434  Value = "%nrbquote(12345)";
21435  Default = "%nrbquote()";
21436  output;
21437  Name = 'Alpha';
21438  Value = "%nrbquote(0.01)";
21439  Default = "%nrbquote()";
21440  output;
21441  Name = 'Pvalue';
21442  Value = "%nrbquote(0.01)";
21443  Default = "%nrbquote()";
21444  output;
21445  Name = 'AdjustFreq';
21446  Value = "%nrbquote(N)";
21447  Default = "%nrbquote()";
21448  output;
21449  Name = 'FreqMiss';
21450  Value = "%nrbquote(N)";
21451  Default = "%nrbquote()";
21452  output;
21453  Name = 'FreqCount';
21454  Value = "%nrbquote(N)";
21455  Default = "%nrbquote()";
21456  output;
21457  Name = 'StratifyCriterion';
21458  Value = "%nrbquote(EQUAL)";
21459  Default = "%nrbquote(PROPORTIONAL)";
21460  output;
21461  Name = 'MinStrataSize';
21462  Value = "%nrbquote(5)";
21463  Default = "%nrbquote()";
21464  output;
21465  Name = 'IgnoreSmallStrata';
21466  Value = "%nrbquote(N)";
21467  Default = "%nrbquote()";
21468  output;
21469  Name = 'ClusterMethod';
21470  Value = "%nrbquote(RANDOM)";
21471  Default = "%nrbquote()";
21472  output;
21473  Name = 'LevelProportion';
21474  Value = "%nrbquote(100)";
21475  Default = "%nrbquote()";
21476  output;
21477  Name = 'LevelSampleProportion';
21478  Value = "%nrbquote(50)";
21479  Default = "%nrbquote()";
21480  output;
21481  Name = 'LevelSelection';
21482  Value = "%nrbquote(EVENT)";
21483  Default = "%nrbquote()";
21484  output;
21485  Name = 'OutputType';
21486  Value = "%nrbquote(DATA)";
21487  Default = "%nrbquote()";
21488  output;
21489  Name = 'IntervalDistribution';
21490  Value = "%nrbquote(Y)";
21491  Default = "%nrbquote()";
21492  output;
21493  Name = 'ClassDistribution';
21494  Value = "%nrbquote(Y)";
21495  Default = "%nrbquote()";
21496  output;
21497  run;
 
NOTE: The data set WORK.PROPERTIES has 20 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     427
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21498  proc sort data=properties;
21499  by Name;
21500  run;
 
NOTE: There were 20 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK.PROPERTIES has 20 observations and 3 variables.
NOTE: PROCEDURE SORT used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     111
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21501  data temp;
21502  length Name $32 Value $200 Default $200;
21503  label NAME = "%sysfunc(sasmsg(sashelp.dmine, rpt_property_vlabel, NOQUOTE))" VALUE =
21503! "%sysfunc(sasmsg(sashelp.dmine, rpt_value_vlabel,    NOQUOTE))";
21504  Name = 'Component';
21505  Value = "Sample";
21506  Default="";
21507  run;
 
NOTE: The data set WORK.TEMP has 1 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     313
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
21508  data properties;
21509  set temp properties;
21510  run;
 
NOTE: There were 1 observations read from the data set WORK.TEMP.
NOTE: There were 20 observations read from the data set WORK.PROPERTIES.
NOTE: The data set WORK.PROPERTIES has 21 observations and 3 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     155
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           264
 
 
NOTE: PROCEDURE DISPLAY used (Total process time):
      real time           0.01 seconds
      user cpu time       0.01 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  1
      Page Faults                       0
      Page Reclaims                     1109
      Page Swaps                        0
      Voluntary Context Switches        19
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           1072
 
 
WARNING: Argument 3 to macro function %SUBSTR is out of range.
 
NOTE: The data set WORK._TMPMS_CONTENT has 3 observations and 1 variables.
NOTE: PROCEDURE CONTENTS used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     149
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      1
      Block Input Operations            0
      Block Output Operations           272
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.00 seconds
      system cpu time     0.00 seconds
      memory              40024.28k
      OS Memory           50560.00k
      Timestamp           08/02/2024 03:27:55 AM
      Step Count                        1  Switch Count  0
      Page Faults                       0
      Page Reclaims                     55
      Page Swaps                        0
      Voluntary Context Switches        0
      Involuntary Context Switches      0
      Block Input Operations            0
      Block Output Operations           0
 
 
 
NOTE: There were 3 observations read from the data set WORK._TMPMS_CONTENT.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      user cpu time       0.01 seconds
*****************************************************************************************
NOTE: File view has been truncated.
Refer to /home/u63387163/European Credit Card/Workspaces/EMWS1/Report/EMLOG.log 
on this server for entire file contents.
*****************************************************************************************
