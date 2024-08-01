---
title: "Baek Joon - networkSecurityAssignmentCode"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
This is only for NetworkSecurity Assignment.
To submit full code.

```c
//using pcap api, print information about the packets
//Ethernet Header : src mac / dst mac
//IP Header : src ip / dst ip
//TCP Header : src port / dst port
//only care TCP protocol
//some message
#include <pcap.h>
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>

struct ethheader {
    u_char  ether_dhost[6];    /* destination host address */
    u_char  ether_shost[6];    /* source host address */
    u_short ether_type;                     /* IP? ARP? RARP? etc */
};

/* IP Header */
struct ipheader {
  unsigned char      iph_ihl:4, //IP header length
                     iph_ver:4; //IP version
  unsigned char      iph_tos; //Type of service
  unsigned short int iph_len; //IP Packet length (data + header)
  unsigned short int iph_ident; //Identification
  unsigned short int iph_flag:3, //Fragmentation flags
                     iph_offset:13; //Flags offset
  unsigned char      iph_ttl; //Time to Live
  unsigned char      iph_protocol; //Protocol type
  unsigned short int iph_chksum; //IP datagram checksum
  struct  in_addr    iph_sourceip; //Source IP address
  struct  in_addr    iph_destip;   //Destination IP address
};

/* TCP Header */
struct tcpheader {
    u_short tcp_sport;               /* source port */
    u_short tcp_dport;               /* destination port */
    u_int   tcp_seq;                 /* sequence number */
    u_int   tcp_ack;                 /* acknowledgement number */
    u_char  tcp_offx2;               /* data offset, rsvd */
#define TH_OFF(th)      (((th)->tcp_offx2 & 0xf0) >> 4)
    u_char  tcp_flags;
#define TH_FIN  0x01
#define TH_SYN  0x02
#define TH_RST  0x04
#define TH_PUSH 0x08
#define TH_ACK  0x10
#define TH_URG  0x20
#define TH_ECE  0x40
#define TH_CWR  0x80
#define TH_FLAGS        (TH_FIN|TH_SYN|TH_RST|TH_ACK|TH_URG|TH_ECE|TH_CWR)
    u_short tcp_win;                 /* window */
    u_short tcp_sum;                 /* checksum */
    u_short tcp_urp;                 /* urgent pointer */
};

void got_packet(u_char *args, const struct pcap_pkthdr *header,
                              const u_char *packet)
{
  struct ethheader *eth = (struct ethheader *)packet;
  printf("------------------below is new packet---------------------\n");
    printf("       From: %02x:%02x:%02x:%02x:%02x:%02x\n",
             eth->ether_shost[0],
             eth->ether_shost[1],
             eth->ether_shost[2],
             eth->ether_shost[3],
             eth->ether_shost[4],
             eth->ether_shost[5]);
    printf("         To: %02x:%02x:%02x:%02x:%02x:%02x\n",
             eth->ether_dhost[0],
             eth->ether_dhost[1],
             eth->ether_dhost[2],
             eth->ether_dhost[3],
             eth->ether_dhost[4],
             eth->ether_dhost[5]);

  if (ntohs(eth->ether_type) == 0x0800) { // 0x0800 is IP type
    struct ipheader * ip = (struct ipheader *)
                           (packet + sizeof(struct ethheader)); 
	printf("       From: %s\n", inet_ntoa(ip->iph_sourceip));   
	printf("         To: %s\n", inet_ntoa(ip->iph_destip));    

    if(ip->iph_protocol == IPPROTO_TCP){
        struct tcpheader *tcp = (struct tcpheader *)(packet + sizeof(struct ethheader) + (ip->iph_ihl)*4);
        //sizeof(struct ipheader) is equal to iph_ihl*4?
        printf("   Protocol: TCP\n");
        printf("   Src port: %d\n", ntohs(tcp->tcp_sport));
        printf("   Dst port: %d\n", ntohs(tcp->tcp_dport));
        uint16_t ip_total_length = ntohs(ip->iph_len);
        uint8_t ip_header_length = ip->iph_ihl * 4;
	uint8_t tcp_header_length = ((tcp->tcp_offx2 & 0xF0) >> 4) * 4;
        uint16_t tcp_payload_length = ip_total_length - ip_header_length - tcp_header_length;

        // Print message in the TCP packet
	// failed...
        printf("   Message: ");
        for(int i=0; i<tcp_payload_length; i++){
            printf("%02x", packet[sizeof(struct ethheader) + ip_header_length + tcp_header_length + i]);
        }
        printf("\n");
    }
  }
}

int main(){
  pcap_t *handle;
  char errbuf[PCAP_ERRBUF_SIZE];
  struct bpf_program fp;
  char filter_exp[] = "tcp";
  bpf_u_int32 net;

  // Step 1: Open live pcap session on NIC with name enp0s5
  handle = pcap_open_live("enp0s5", BUFSIZ, 1, 1000, errbuf);

  // Step 2: Compile filter_exp into BPF psuedo-code
  pcap_compile(handle, &fp, filter_exp, 0, net);
  if (pcap_setfilter(handle, &fp) != 0) {
      pcap_perror(handle, "Error:");
      exit(EXIT_FAILURE);
  }

  // Step 3: Capture packets
  pcap_loop(handle, -1, got_packet, NULL);

  pcap_close(handle);   //Close the handle
  return 0;
}

```
