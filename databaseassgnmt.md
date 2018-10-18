# Database Assignment

## Campaign: Ben Cardin for Senate

### Opportunities for support:

**Identify prospective donors who have made contributions to other Democratic candidates but not yet to our campaign**
In order to raise more funds, we should seek to diversify our donor base.  Therefore, we should seek to identify donors who already support Democratic candidates who have not yet contributed to our campaign or to committees that have supported our campaign.

*Query*

First, I would find committees that have supported Democratic candidates other than Ben Cardin:

```
select DISTINCT CMTE_ID from committee2candidate where CAND_ID IN (select CAND_ID from candidate where CAND_PTY_AFFILIATION = "DEM") and CMTE_ID NOT IN (select CMTE_ID from committee2candidate where CAND_ID = "S6MD03177") LIMIT 25;
```

Then I would find Maryland donors who had made contributions to those committees:

```
select * from marylandcontributions where cmte_ID in ("C00411116", "C00010470", "C00242271", "C00076182", "C00186064", "C00238444", "C00382028", "C00497933", "C00513531", "C00195628", "C00494203", "C00556266", "C00557256", "C00258475", "C00409219", "C00336057", "C00428094", "C00393769", "C00292094", "C00280743", "C00078535", "C00040253", "C00142711", "C00340455", "C00397067");
```

And I would want to exclude Maryland donors who had contributed to a Cardin-supporting committee, whom I would find using this query:

```
select * from marylandcontributions where CMTE_ID IN (select CMTE_ID from committee2candidate where CAND_ID = "S6MD03177");  
```

*Note: I was not able to figure out how to combine these three queries in MySQL.  In addition, the server timed out trying to run the first query therefore I limited this to 25 rows, which impacts all subsequent queries as they are only running off of a subset of the relevant committees.*

**Identify leaders involved with multiple committees supporting Democratic candidates**

In terms of building coalitions, we’ll want to focus on those who are influential.  Individuals involved with multiple committees in a leadership role, such as a Treasurer, are probably very connected politically as well as clearly very politically engaged.  We should seek to identify these individuals and reach out to them about how we can work together.

*Query*

I would find committees that had supported Democratic candidates and then order the results by their Treasurer’s name in order to see where the same individual was involved with multiple committees.

```
select * from committee where cmte_ID IN (select cmte_ID from committee2candidate where cand_ID in (select CAND_ID from candidate where CAND_PTY_AFFILIATION = "DEM")) order by tres_nm desc;
```

**Potential issue with the data:**
In the Committees table, the Treasurer names are formatted inconsistently.  Some are “First Last” and some are “Last, First.”  Some are listed with “M.D.” as a suffix while others are listed with “Dr.” as a suffix.  There also appear to be some spaces in this column.  These data will need to be cleaned and standardized before attempting to conduct any analysis looking for relationships based on common individuals (by name).
